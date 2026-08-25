---
name: python-configuration-resilience
description: Configuration and resilience for an APIMatic-generated Python SDK — what the timeout actually bounds, the fact that the SDK performs NO retries and what that means for you to build, base-URL selection, proxies and TLS, request/response logging through the transport seam, and idempotency for writes. Load before you configure or tune the client — the keyword names alone do not reveal what is and is not handled for you.
---

# Configuration & resilience for an APIMatic Python SDK

> `{...}` is a placeholder for a name from your SDK.

## There are no retries. None.

**This is the headline fact, and it is the opposite of what the .NET generator does.** The SDK ships no
retry policy, no backoff, no Polly-equivalent pipeline. An architectural decision record rules retries
out of the request path deliberately. Concretely:

- A `429` or `503` raises on the first attempt. Nothing is resent.
- A connection reset raises. Nothing is resent.
- Even the one place a retry would look automatic — a `401` — is not one: the cached token is
  invalidated so the *next* call re-authenticates, and the failing request is deliberately not
  retried. You see one `401`, then recovery.

Two implications:

1. **Whatever retrying your integration needs, you write** — outside the SDK call. There is no knob.
2. **You inherit no accidental duplicate writes**, which is the flip side and genuinely valuable. In the
   .NET SDK a transport failure resends a `POST` on any verb; here a failed write was attempted exactly
   once at the SDK layer. Do not give that property away carelessly when you add retries.

### Adding retries yourself

Use `tenacity` (or `backoff`, or a loop), wrap **your** call, and decide per verb:

```python
import httpx
from tenacity import retry, retry_if_exception, stop_after_attempt, wait_exponential_jitter
from {root_package}.core import ApiError

def _is_transient(e: BaseException) -> bool:
    if isinstance(e, httpx.TimeoutException | httpx.ConnectError):
        return True
    return isinstance(e, ApiError) and e.status_code in {429, 500, 502, 503, 504}

@retry(
    retry=retry_if_exception(_is_transient),
    stop=stop_after_attempt(3),
    wait=wait_exponential_jitter(initial=1, max=10),
    reraise=True,
)
def get_order(order_id: str):
    return client.orders.get_order(order_id)     # a GET: safe to retry
```

Rules to hold to:

- **Retry idempotent reads freely; treat writes as a separate decision.** A `POST` that timed out may
  have succeeded. Retrying it can double-charge.
- **Send an idempotency key on writes that accept one — and check first, because half of them do
  not.** Only **12 of the 26 write operations** declare `pay_pal_request_id`. The same value on a
  resend lets the provider collapse the duplicate:

  ```python
  request_id = str(uuid.uuid4())          # once per logical order, reused by every attempt
  client.orders.create_order(body, pay_pal_request_id=request_id)
  ```

  The 12 that accept one: `create_order`, `authorize_order`, `capture_order`,
  `capture_authorized_payment`, `reauthorize_payment`, `refund_captured_payment`, `void_payment`,
  `create_subscription`, `capture_subscription`, `create_billing_plan`, `create_payment_token`,
  `create_setup_token`.

  **The other 14 writes have no idempotency parameter at all** — `confirm_order`,
  `create_order_tracking`, `patch_order`, `update_order_tracking`, `revise_subscription`,
  `activate_subscription`, `suspend_subscription`, `cancel_subscription`, `patch_subscription`,
  `activate_billing_plan`, `deactivate_billing_plan`, `patch_billing_plan`,
  `update_billing_plan_pricing_schemes`, `delete_payment_token`. For these, a retried write cannot
  be deduplicated by the provider, so **retrying one is a real duplication risk** and the safe
  recovery is to re-read state and decide, not to resend. Several are idempotent by nature (a second
  `cancel_subscription` on a cancelled subscription is harmless), but that is a per-operation
  judgement — make it deliberately rather than retrying the class.

  Whether the provider enforces the key is a live-traffic fact, not something the signature tells you
  — PayPal documents it as required for single-step creates carrying a payment source. The key's
  retention window also differs by API (the orders operations document 6 hours, extensible to 72;
  the subscriptions ones document 72), so a resend after a long backoff may no longer be collapsed.
- **Never retry a `ValidationError`** or a 4xx other than `429`. They cannot succeed on a second try.
- **Respect `Retry-After`** on a `429` when present: `e.response.headers.get("retry-after")` (keys are
  lowercased).
- Cap your worst case — `attempts × timeout + backoff` — below whatever deadline your own caller works
  to, or you are burning provider capacity for a response nobody is waiting for.

## Timeouts — the one knob, in two places

```python
client = {Api}Client(timeout=10.0, oauth2=...)                       # every request
client.orders.get_order(id, request_options={"timeout": 3.0})         # this one request
```

- The value is **seconds, as a float**, and the per-call option overrides the client's.
- It must be **> 0**; the client constructor raises `ValueError` otherwise, at construction.
- The default is a **30-second** client timeout. That is far more sensible than the .NET SDK's 100s, but
  it is still too long for anything on a user-facing request path — set it deliberately.
- Under the httpx transport, one float sets connect, read, write and pool timeouts alike. If you need
  them separated (a short connect, a longer read), build your own transport with an
  `httpx.Timeout(connect=..., read=...)` and pass it as `custom_http_client`.

**Because there are no retries, the timeout genuinely bounds the call.** This is the simplification the
no-retry design buys you: worst case is one timeout, not attempts × timeout. The moment you add
retries, that stops being true and the arithmetic above is yours to do.

**If you pass `custom_http_client`, the client's `timeout=` no longer reaches the wire** — it is used
only to build the SDK's own default transport. Set the timeout on the transport you supply.

For async callers, a deadline over a whole operation (SDK call plus your own work) is
`asyncio.timeout(...)`; it raises `TimeoutError` through the await, which no `except ApiError` catches.

## Base URL / environment

Where the SDK models environments as a single `base_url` (rather than an environment enum), selection
and override are the same knob:

```python
client = {Api}Client(base_url="https://api-m.paypal.com", oauth2=...)   # live
client = {Api}Client(oauth2=...)                                        # default (sandbox here)
```

Three things to state in any config layer:

- **The default is sandbox.** Omitting `base_url` in production is a silent misconfiguration — nothing
  warns you, and live credentials against sandbox fail auth in a way that looks like a credentials
  problem.
- **The token endpoint follows `base_url`**, so environment and auth can never drift apart.
- Point `base_url` at a mock server or a recording proxy to test against a fake. There is no separate
  "mock mode".

Resolve the URL from configuration with an explicit map, and fail on an unknown value rather than
defaulting:

```python
BASE_URLS = {"sandbox": "https://api-m.sandbox.paypal.com", "live": "https://api-m.paypal.com"}
base_url = BASE_URLS[settings.paypal_env]      # KeyError beats a silent wrong environment
```

## Proxies and TLS

The default httpx-backed transport takes these directly:

```python
from {root_package}.core import HttpxClient

transport = HttpxClient(timeout=10.0, proxy_url="http://proxy:3128", verify=True)
client = {Api}Client(custom_http_client=transport, oauth2=...)
```

- `verify` accepts a bool **or an `ssl.SSLContext`** — a private CA bundle or a client certificate is
  configured by building a context (`ssl.create_default_context(cafile=...)`), which is the supported
  spelling for either.
- **Standard environment variables are honoured by default**: `HTTP_PROXY` / `HTTPS_PROXY` / `ALL_PROXY`
  / `NO_PROXY` (consulted only when `proxy_url` is unset) and `SSL_CERT_FILE` / `SSL_CERT_DIR`. This is
  request behaviour your deployment environment can change without touching code — worth knowing when a
  container behaves differently from a laptop. A caller who needs it off supplies their own transport.
- **`verify=False` disables certificate verification.** It is not a debugging convenience for anything
  carrying credentials; fix the trust store instead.

## Logging — wrap the transport

There is no logging hook. The transport protocol is the seam: implement it, delegate to the real one,
and log around the call.

```python
import logging, time
from {root_package}.core import HttpxClient

log = logging.getLogger(__name__)

class LoggingTransport:
    def __init__(self, inner): self._inner = inner

    def send(self, request):
        started = time.monotonic()
        response = self._inner.send(request)
        log.info(
            "%s %s -> %s (%.0f ms)",
            request.method, request.url, response.status_code,
            (time.monotonic() - started) * 1000,
        )
        return response

    def close(self): self._inner.close()

client = {Api}Client(custom_http_client=LoggingTransport(HttpxClient(timeout=10.0)), oauth2=...)
```

The async version is the same shape with `async def send` and `async def aclose`.

**Log the method, URL and status — not headers or bodies.** The `Authorization` header carries a bearer
token and the bodies carry payment data; both belong in neither your logs nor your traces. If you must
capture a body for debugging, gate it behind a flag that is off by default and redact before writing.

This same wrapper is where OpenTelemetry spans, metrics and request-id propagation belong.

**Run it on the first execution of any new call.** On success the SDK returns only the decoded body, so
a wrong path parameter or a query param that silently did not serialize produces no in-band signal —
the only symptom is a `404`/`422` that looks like an API problem. Check the printed request:

1. the **verb** matches the operation;
2. the **path** has no unsubstituted `{placeholder}`;
3. path segments carry **wire values** (the enum's string, not a Python member name);
4. the query params you set actually appear.

Then gate the handler behind a debug flag.

## Connection pooling

The client holds one pooled transport. Reuse the client
(`python-client-initialization`) and close it on shutdown; a client per call pays a fresh TCP+TLS
handshake and a fresh token fetch every time. Under forking servers (Gunicorn, uWSGI, Celery
prefork), construct it **after** the fork — a pool inherited across `fork()` is shared by processes
that each believe they own it, which surfaces as intermittent, unexplainable connection errors.

## Next

- Where the client should live → **python-client-initialization**
- Which exceptions actually reach your boundary → **python-error-handling**
- Faking the transport in tests → **python-testing**
