---
name: python-error-handling
description: Error handling for an APIMatic-generated Python SDK — load before writing any try/except around an SDK call, an exception-translation layer, or error middleware. Covers the single ApiError type and its per-operation error union, narrowing with isinstance, the decode failures that are NOT ApiError and bypass both response modes, httpx exceptions reaching your boundary unwrapped, and presenting failures without leaking internals.
---

# Error handling for an APIMatic Python SDK

> `{...}` is a placeholder for a name from your SDK.

Operations **raise on non-2xx responses** by default. The design here is different from the .NET
generator's and simpler in one respect, more subtle in another: there is **one exception class**, and
the interesting variation lives in the payload it carries.

```python
from {root_package}.core import ApiError, RawError
```

`ApiError` is generic in its payload (`ApiError[E]`), but you catch it **unparametrized** — a runtime
`except` clause cannot discriminate a generic parameter, so `except ApiError` catches every operation's
failure and you narrow on `e.error` afterwards.

## What the exception carries

| Attribute | Type | Notes |
|---|---|---|
| `e.error` | the operation's error union | The decoded body. **This is the information.** |
| `e.response` | `HttpResponse` | The SDK's own response type — `status_code`, `headers`, `content`, `text()`, `json()` |
| `e.status_code` | `int` | Shortcut for `e.response.status_code` |

**`str(e)` and `repr(e)` deliberately omit the body.** They render as
`HTTP 422: Error` — the status and the payload's *type name* only, so response bodies stay out of logs
and tracebacks by default. That is a sound default and a trap for the unwary: a handler that logs
`str(e)` and nothing else records that something failed and discards every diagnostic. Read `.error`
explicitly and log the fields you want.

## The error union, and narrowing it

Each operation declares which schemas it maps, so its error type is a **union**, typically
`{TypedError} | RawError`:

- the **typed** arm for statuses the operation documents (decoded into a model);
- **`RawError`** for any other status — no declared schema, so the body is handed over undecoded.

**The typed arm is not one type across the API, and assuming it is is the mistake that costs the
afternoon.** An API description that declares a different error schema per tag gets a different model
per tag, and this SDK is that case — it has **four** distinct error bodies plus one operation with no
typed arm at all:

| Typed arm | Where | Notes |
|---|---|---|
| `Error` | `orders` (8 ops), `payments` (7) | `details[ErrorDetails]`, `links[LinkDescription]` |
| `Error1` | `vault` (6) | same members, but `details[ErrorDetails1]` / `links[ErrorLinkDescription]` |
| `SubscriptionError` | `subscriptions` (17) | adds `information_link` |
| `DefaultError` | `search_balances` | adds `information_link`; `details[TransactionSearchErrorDetails]` |
| *none* | `search_transactions` | `ApiError.error` is **always** `RawError` |

So `isinstance(e.error, Error)` matches 15 of 40 operations; write it as your only typed check and
every subscriptions and vault failure falls through to the `RawError` branch and loses its
`name`/`message`/`debug_id`. Two ways to handle it, and the choice is real:

- **Narrow structurally** where your boundary treats all provider rejections alike. All four models
  declare `name`, `message` and `debug_id` as **required**, so one clause reads every one of them:

  ```python
  from {root_package}.models import DefaultError, Error, Error1, SubscriptionError

  ProviderError = (Error, Error1, SubscriptionError, DefaultError)   # all carry name/message/debug_id

  if isinstance(e.error, ProviderError):
      log.warning("%s: %s (debug_id=%s)", e.error.name, e.error.message, e.error.debug_id)
  ```

- **Narrow per operation** where you act on the payload differently. Take the exact arm from the
  contract sheet — never guess it from another operation in the same client.

The contract sheet lists the union per operation (and it is also in the operation's docstring, as
`` `error` is `Error | RawError` ``). Narrow with `isinstance`, which the type checker understands:

```python
from {root_package}.core import ApiError, RawError
from {root_package}.models import Error

try:
    order = client.orders.create_order(body)
except ApiError as e:
    match e.error:
        case Error() as api_error:
            log.warning(
                "rejected: %s / %s (debug_id=%s)",
                api_error.name, api_error.message, api_error.debug_id,
            )
            for detail in api_error.details or []:
                log.warning("  issue=%s field=%s", detail.issue, detail.field)
        case RawError() as raw:
            log.error("HTTP %s: %s", raw.status_code, raw.text())
```

`RawError` gives you `status_code`, `content`, `text()` and `json()`. **`json()` raises `ValueError`
when the body is not JSON** — and a `RawError` body often is not (that is the no-declared-schema case),
so prefer `text()` for logging unless you know better.

Unlike the .NET generator, the HTTP status is **always available** on `e.status_code` regardless of
which arm you got, so you never have to choose between a typed body and knowing the status. That is
also the fallback for the operations with no typed arm: `search_transactions` gives you the status and
`RawError.text()`, and nothing more.

**A `2xx`-only operation still raises.** 11 of the 40 operations return `None` on success
(`patch_order`, `update_order_tracking`, `delete_payment_token`, and the eight mutating
`subscriptions` operations). A `None` return is *success*, not a failure to check for — the failure
path is still the exception. Never write `if client.subscriptions.cancel_subscription(...) is None:`
as an error test; it is always `None`.

## The failures that are NOT `ApiError`

This is the section to get right; each of these reaches your boundary without matching
`except ApiError`.

### 1. Decode failures — and they bypass *both* response modes

If the response body does not match the declared schema, decoding raises
`pydantic.ValidationError` (a `ValueError`), or plain `ValueError` if the body is not JSON at all. The
pipeline states the rule outright: *a deserialization failure is not an API error, so it propagates in
both response modes rather than becoming a failure.* So `with_raw_response` does **not** convert it
into a `Failure`.

It arrives from two directions that mean opposite things:

- **A 2xx whose body drifted** → the call may well have *succeeded* server-side and you cannot read
  the result. The outcome is **unknown**. For a write, the only safe reading is "this may have taken
  effect" — re-read state rather than retrying blindly.
- **A non-2xx whose body doesn't match the operation's error schema** → the request was
  **rejected**; only the detail was lost. Retrying is pointless, since it can never succeed.

**In this SDK the 2xx direction is far narrower than it sounds, and the gap it leaves is worse.**
16 of the 17 non-`None` return types declare **no required member at all** — `Order`,
`Subscription`, `BillingPlan` and the rest all validate an empty `{}` successfully. Only
`SubscriptionTransactionDetails` (from `capture_subscription`) requires anything: `id`,
`amount_with_breakdown`, `time`.

So a missing field on a success response does **not** raise. It reads back as `UNSET`:

```python
order = client.orders.create_order(body)   # 200 with a truncated / empty body
order.id                                   # UNSET — not an exception, not None
```

That inverts where the guard belongs. A `ValidationError` on a 2xx here means a *type* mismatch or a
non-JSON body (a scalar where an object was declared, a string where a list was), not an absent
field — so the failure you should actually defend against on the success path is the quiet one:

```python
order = client.orders.create_order(body)
if order.id is UNSET:                      # the response was not what we needed
    raise ProviderUnreadable("create_order returned no id; outcome unknown")
```

**Assert on the members you depend on, immediately after every call that matters.** For a write, an
absent id has the same "outcome unknown" character as a decode failure — the call may have moved
money and you cannot name what it created. Treat it the same way; nothing in the SDK will do it
for you.

Mapping both to a 5xx is wrong half the time. The status is still recoverable in the second case —
catch `ValidationError` separately and, if you need the status, use `with_raw_response` (whose
`Failure` you never reach) or read it from a transport-level wrapper. At minimum, do not report a
deterministic rejection as an outage.

### 2. Transport failures — httpx exceptions, unwrapped

The SDK does not wrap its HTTP library's exceptions: connection refused, DNS failure, TLS error,
dropped socket and timeout all surface as **`httpx` exceptions**, propagating through the SDK
untouched.

```python
import httpx

except httpx.TimeoutException as e:   # includes ConnectTimeout / ReadTimeout
    ...
except httpx.HTTPError as e:          # base class for the rest of httpx's failures
    ...
```

`httpx.HTTPError` is the catch-all base; catch `TimeoutException` first when a timeout deserves
different handling. **A write that fails this way has an unknown outcome** — a connection reset after
the bytes reached the server is indistinguishable from one before.

Note the coupling this creates: your error boundary imports `httpx` because the SDK's transport does.
If you swap in a custom transport (`python-client-initialization`), it is *your* transport's exceptions
that arrive, and this clause needs revisiting.

### 3. Authentication failures wear the same exception

A failed token fetch raises `ApiError` — but its payload is `OAuthProviderError | RawError`, **not** the
operation's union, and it surfaces out of the operation call because the token is fetched lazily. It
also raises in `with_raw_response` mode. Check it **first**, because nothing was ever sent:

```python
from {root_package}.core import OAuthProviderError

except ApiError as e:
    if isinstance(e.error, OAuthProviderError):
        raise ConfigurationError(f"credentials rejected: {e.error.error}") from e
    ...
```

See `python-authentication` for the full picture.

### 4. Your own mistakes

`ValidationError` from constructing a model with a missing or wrong-typed member, and `TypeError` from
a positional argument to a keyword-only constructor, are programming errors. Let them fail loudly in
development; do not add them to a production catch ladder that swallows them.

## The complete ladder

Order matters — most specific first, and auth before the operation's own union:

```python
import httpx
from pydantic import ValidationError
from {root_package}.core import ApiError, OAuthProviderError, RawError
from {root_package}.models import DefaultError, Error, Error1, SubscriptionError

# Every typed arm this API declares. All four carry required name/message/debug_id.
PROVIDER_ERRORS = (Error, Error1, SubscriptionError, DefaultError)

try:
    order = client.orders.create_order(body)

except ApiError as e:
    if isinstance(e.error, OAuthProviderError):
        raise ProviderConfigError("PayPal credentials rejected") from e      # 5xx: our misconfig
    if isinstance(e.error, PROVIDER_ERRORS):
        raise ProviderRejected(e.status_code, e.error.name, e.error.message) from e   # map 4xx -> 4xx
    raise ProviderFailure(e.status_code, e.error.text()) from e              # RawError arm

except ValidationError as e:
    raise ProviderUnreadable("unreadable response; outcome unknown") from e  # do NOT assume failure

except httpx.HTTPError as e:
    raise ProviderUnavailable("provider unreachable; outcome unknown") from e
```

Always `raise ... from e`. Losing the `__cause__` costs you the traceback that names which of the four
paths you were actually on.

## Presenting failures at your boundary

**Keep distinct failures distinct — carry the provider's status.** A provider **4xx** (validation,
conflict, not-found) is actionable by your caller and should surface as a client 4xx. A transport
failure or an unknown error has no meaningful client status and belongs at 5xx. Collapsing everything
into one blanket status discards the only signal that separates "you sent something invalid" from
"the provider is down".

**Never surface `str(e)` or a traceback to your caller.** For an SDK exception the string is only
`HTTP 422: Error`, so it is useless as well as leaky; for a `ValidationError` it embeds pydantic type
and field-path detail. Log the detail, return a message you wrote.

**Never map a parse failure onto a domain absence.** "I could not read the answer" is not "the provider
said no". On a lookup, an unreadable body and a genuine miss both leave you without a record, but only
one of them is a fact — and where a lookup gates a create, conflating them turns a corrupt response
into a spurious create. If a miss is signalled by an empty body, match on *empty*, not on
*unparseable*.

**Guard reads, not just writes.** It is easy to wrap the calls that move money and leave the `get_order`
on a status page unguarded. A connection failure during a read fails just as hard.

## Notes

- **There are no retries in this SDK** — no status is retried, and a `401` invalidates the cached token
  without resending the request. Anything you want retried, you build (`python-configuration-resilience`).
- `ApiError` is picklable, so it survives crossing a process boundary (a Celery result, a multiprocessing
  queue) with its payload intact.
- `e.response.headers` has **lowercased keys**, guaranteed by the transport contract — look up
  `"paypal-debug-id"`, never `"PayPal-Debug-Id"`.
