---
name: python-getting-started
description: PayPal Python SDK identity and lookup layer (Python only) — install, import root, base URL/environments, the auth pattern, and the module map that names the one file owning each kind of contract fact. Load this before answering any PayPal Python SDK contract question or writing any SDK code.
---

# Getting started with the PayPal Python SDK

> **Who this skill is for.** This is the **lookup layer** for anyone writing PayPal Python SDK code —
> it is yours to follow directly and fully. Ground every contract fact here (and in the source
> modules the map below names) rather than in recall, and carry those facts onto a contract sheet
> before you implement. Load `python-integrate-paypal` for the workflow that wraps this skill.

This is the **SDK-specific** entry point. For general patterns that apply to any APIMatic-generated
Python SDK (client construction, auth, calling endpoints, models, error handling, resilience,
testing), see the companion API-agnostic skills: `python-client-initialization`,
`python-authentication`, `python-calling-endpoints`, `python-models`, `python-error-handling`,
`python-configuration-resilience`, `python-testing`.

**This page and those companion skills are complementary — load both.** This page is authoritative
for the SDK's *identity and surface* (what to install, what to import, the controllers, which module
owns which fact); the companion skills are the *usage layer* on top — the best-practice way to call
each piece and the gotchas a signature can't show. Reading a module in the installed package doesn't
remove the need to load the skill for that step, so at each step below, load the companion *and*
confirm names against the installed package.

## SDK identity

Verified against `pay_pal_server_sdk/` and `pyproject.toml` of the generated package at version
`2.29`. **Re-verify after a version bump** — this page is a snapshot, not a live read.

| Fact | Value |
|---|---|
| API | PayPal |
| Distribution name (what you install) | `pay-pal-server-sdk` — **not on any package index**; installed from source (see *Install*) |
| Import root (what you import) | `pay_pal_server_sdk` — note the underscores; the two names differ |
| Source repo | https://github.com/mehnoorsiddiqui/paypal-sdk-v4 |
| Version | `2.29` |
| Sync client class | `PayPalServerSdkClient` (alias `Client`) |
| Async client class | `AsyncPayPalServerSdkClient` (alias `AsyncClient`) |
| Client construction | **keyword-only**: `base_url`, `timeout` (default `30.0`), `oauth2`, `oauth2_token_source`, and the transport override — `custom_http_client` on the sync client, `custom_async_http_client` on the async one (the names differ; see step 1) |
| Auth | one scheme — OAuth 2.0 client credentials via `oauth2=ClientCredentials(...)` **or a plain dict**; token fetched and cached by the client itself (see *Auth pattern*) |
| Environments | **no environment enum** — one `base_url` string, defaulting to sandbox (see *Environments*) |
| Base-URL config | `ServerConfig` (`server/server_config.py`), frozen, `extra="forbid"` |
| Python floor | **`>=3.10`** (classifiers list 3.10–3.14) |
| Runtime dependencies | `httpx>=0.28.1,<1.0.0` · `pydantic[email]>=2.11.0,<3.0.0` · `typing-extensions>=4.13.0,<5.0.0` |
| Typing | ships `py.typed`; the package is checked under `mypy --strict` with `warn_unreachable`. Callers get full inference — **a type error against this SDK is a real contract violation, not noise** |
| Line length / lint | `ruff`, 120 cols (only relevant when editing the SDK itself) |
| Surface | 40 operations across 5 controllers · 286 models · 85 enums · 39 per-operation error unions |

The table above is **orientation, not a copy-paste recipe** — it gives you the names and facts
(install, import roots, the auth *pattern*, the base-URL knob), while the actual integration code
comes from the companion skills. Load each one as you reach its step (see **Integration workflow**
below) and confirm its types against the installed package.

## Install — from source

This SDK is not published to a package index, so there is no `pip install` from PyPI for it. Install
it from its repository — <https://github.com/mehnoorsiddiqui/paypal-sdk-v4> — into the same
environment your project runs in:

```bash
pip install "pay-pal-server-sdk @ git+https://github.com/mehnoorsiddiqui/paypal-sdk-v4.git"
```

The generated distribution carries its own `pyproject.toml`, so `pip` builds and installs it exactly
like a released package. Do not vendor its source into your project, add its directory to `sys.path`,
or install it editable (`-e`) from a throwaway clone — an editable install points at the clone's
path, so deleting the clone breaks every import. Once installed, write the imports from the table
above: the distribution name you install and the package name you import are not the same string.
Requires Python 3.10 or newer.

## Imports — the package splits its surface across four modules

Python does not re-export child modules transitively, so `from pay_pal_server_sdk import models`
alone does **not** make enums, error unions, or runtime types reachable. Import each kind of type
from the module that owns it.

`pay_pal_server_sdk/__init__.py` exports exactly six names:

```python
from pay_pal_server_sdk import (
    PayPalServerSdkClient,       # the sync client
    Client,                      # alias for PayPalServerSdkClient
    AsyncPayPalServerSdkClient,  # the async client
    AsyncClient,                 # alias for AsyncPayPalServerSdkClient
    ServerConfig,                # base-URL configuration
    models,                      # the models subpackage
)
```

Everything else comes from its own subpackage, and the split matters because the four places a
caller reaches for are four different modules:

| What you need | Where it lives |
|---|---|
| Domain models, their `…Dict` companions | `pay_pal_server_sdk.models` |
| Enums (and their open `…OrStr` aliases) | `pay_pal_server_sdk.models.enums` |
| `ApiError`, `RawError`, `Success`, `Failure`, `ApiResult`, `RequestOptions`, `ClientCredentials`, `HttpClient`/`AsyncHttpClient`, `HttpxClient`, `OAuthProviderError`, `SdkBaseModel`, `UNSET`, `Optional`, `OptionalNullable` | `pay_pal_server_sdk.core` |
| Per-operation error *unions* (`CreateOrderErrorBody`, …) | `pay_pal_server_sdk.errors` |

`pay_pal_server_sdk.core` re-exports its whole public surface (a curated `__all__`), so import from
`…core` rather than from the private modules beneath it (`…core.results`, `…core.exceptions`,
`…core.auth.schemes`).

## Environments — there is no environment enum

Unlike the .NET SDK, this SDK has **no `ServerEnvironment` type and no environment constants**. There
is one knob: `base_url`, on `ServerConfig` (`server/server_config.py`), and its default is
**sandbox**:

```python
base_url: str = "https://api-m.sandbox.paypal.com"
```

Consequences to state on every contract sheet that touches configuration:

- Omitting `base_url` gives you **sandbox**, silently. A caller who believes they configured live and
  did not gets sandbox behaviour with live credentials — which fails auth rather than moving money,
  but the diagnostic looks nothing like "wrong environment".
- Live is `https://api-m.paypal.com`, passed explicitly as `base_url`.
- The token endpoint is derived from the same `base_url` (`/v1/oauth2/token`), so it always follows
  the environment — you never configure it separately.
- `ServerConfig` is a frozen pydantic model with `extra="forbid"`: a misspelled keyword raises
  `ValidationError` at construction rather than being ignored.
- `timeout` is validated too — `BasePayPalServerSdkClient` raises `ValueError` for any non-positive
  value.

## Auth pattern (one scheme)

The API declares exactly one scheme: OAuth 2.0 client credentials, exposed as the client's `oauth2=`
keyword taking `ClientCredentials` **or a plain dict**. The client fetches and caches the bearer
token itself, lazily, from `<base_url>/v1/oauth2/token` using HTTP Basic client authentication
(RFC 6749 §2.3.1 `client_secret_basic`).

```python
from pay_pal_server_sdk import Client
from pay_pal_server_sdk.core import ClientCredentials

client = Client(oauth2=ClientCredentials(client_id=..., client_secret=...))
client = Client(oauth2={"client_id": ..., "client_secret": ...})   # equivalent
```

**`oauth2=` is optional at the type level and that is a trap worth flagging on every sheet.** Omit it
and the client is built with `no_auth`: every request goes out unauthenticated and PayPal answers
`401`. Nothing fails at construction. `oauth2_token_source` overrides token acquisition itself
(a `TokenSource[ClientCredentials]`, or `AsyncTokenSource` on the async client) — the seam to reach
for when tokens are minted elsewhere. See `python-authentication` for the full picture, including
what a *failed token fetch* raises — it is not what a caller expects, and it is the single most
common surprise in this SDK.

## Controllers

Controllers and their operation counts (`client.<attr>`):

| Attribute | Class | Ops | Area |
|---|---|---|---|
| `client.orders` | `Orders` / `AsyncOrders` | 8 | Orders v2 — create, confirm, capture, authorize, get, patch, tracking |
| `client.payments` | `Payments` / `AsyncPayments` | 7 | Payments v2 — authorizations, captures, refunds, void, reauthorize |
| `client.subscriptions` | `Subscriptions` / `AsyncSubscriptions` | 17 | Subscriptions + billing plans v1 |
| `client.vault` | `Vault` / `AsyncVault` | 6 | Payment method tokens v3 (**US only**) |
| `client.transaction_search` | `TransactionSearch` / `AsyncTransactionSearch` | 2 | Transaction search + balances v1 |

Every controller has an `Async…` peer whose operations are identical in name and parameters and
differ solely by being awaited. Do not emit a separate row for an async operation; state the rule
once on the sheet.

## Contract facts — read the installed package

Read the one module that owns the fact **inside the installed package**. Locate it first:

```bash
python -c "import pay_pal_server_sdk, pathlib; print(pathlib.Path(pay_pal_server_sdk.__file__).parent)"
```

Failing that, it is under the project's environment (`.venv/Lib/site-packages/pay_pal_server_sdk` on
Windows, `.venv/lib/python3.*/site-packages/pay_pal_server_sdk` elsewhere). **If the package is not
installed, there is no source to read** — mark the fact `UNVERIFIED` and say what would settle it
rather than answering from memory. Paths below are relative to that package root:

| Question | Module |
|---|---|
| An operation's real signature, parameters and return type | `apis/<controller>.py` |
| Client construction, keywords, controller wiring | `client.py`, `async_client.py`, `base_client.py` |
| Timeout default and validation | `base_client.py` (`DEFAULT_TIMEOUT = 30.0`) |
| The request/response pipeline, 401 handling, 2xx-vs-error split | `core/raw_client.py` |
| Exception shape (`ApiError.error`, `.response`, `.status_code`) | `core/exceptions.py` |
| `Success`/`Failure`/`RawError` | `core/results.py` |
| Per-call overrides | `core/request_options.py` |
| `UNSET`, `Optional`, `OptionalNullable`, `strip_unset` | `core/optionality.py` |
| Model base config, `to_dict`/`to_json` | `core/models.py` |
| A model's members, required vs `UNSET`, wire aliases | `models/<model_name>.py` |
| An enum's members and wire values | `models/enums/` |
| Open-enum coercion | `core/converters/open_enum.py` |
| Transport protocols (the test seam) | `core/transport.py` |
| httpx adapter, proxy/TLS knobs | `core/httpx_transport.py` |
| Token fetch, credential placement | `core/auth/schemes/oauth2_client_credentials.py`, `core/auth/models.py` |
| Base-URL resolution | `server/server_config.py`, `server/server.py` |
| An operation's error mapper (status → schema) | `errors/<operation>_error.py` |

**Read scoped.** These modules carry long design docstrings; `grep -n` for the symbol and read the
surrounding lines rather than whole files. Never quote a docstring's design rationale onto a contract
sheet — the sheet carries facts an implementer must obey, not the reasoning behind them.

Keep lookups cheap — the rules that keep a session's context small:

- Collect the contracts for **every** in-scope operation in **one** pass — signature, required
  members with wire aliases, the error union, enum values — into a short **contract sheet** in your
  plan or working notes, then implement from the sheet. Don't re-open a module per member, and never
  re-look-up a fact the sheet already carries.
- Recurse into a model's members only where the task actually sets them — a full transitive expansion
  of a PayPal model is hundreds of rows and nobody needs it.
- Trust the interpreter over this page: if a name here ever fails to type-check or import, re-read
  the module the table above names and report the drift; never patch around it from memory.

## Integration workflow — load the companion skill at each step

Before you write the code for each step, load the named companion skill — even if you've already
read the relevant module. Each step calls out the trap the signature hides (in *parens*). A typical
integration reaches them in this order:

1. **Client construction & lifetime** — load **python-client-initialization** before you write
   `Client(...)` or `AsyncClient(...)`. (*The signature won't tell you:* the constructor is
   keyword-only, so nothing can be passed positionally; the client owns an `httpx` connection pool
   and you **must** `close()` (sync) or `await aclose()` (async) or use it as a context manager;
   it must be long-lived and module- or app-scoped, never rebuilt per request; the sync and async
   clients do not mix; and the transport-override keyword differs by client —
   `custom_http_client` vs `custom_async_http_client`.)
2. **Authentication** — load **python-authentication** before you set credentials. The one scheme is
   `oauth2=`, taking `ClientCredentials` or a dict. (*The signature won't tell you:* `oauth2=` is
   *optional* — omit it and every request goes out unauthenticated and gets a `401`, with no
   failure at construction; the token is fetched lazily and cached by the client; and a **failed
   token fetch** raises something other than what a caller expects and **bypasses the non-raising
   response mode** entirely. Load secrets from the environment or a secret store, never hardcode.)
3. **Calling an endpoint** — load **python-calling-endpoints** before the first
   `client.<controller>.<operation>(...)` call. (*The signature won't tell you:* every operation
   splits positional path params (and sometimes the body) from a keyword-only tail after `*`; every
   keyword-only parameter has a **real** default, so unlike the .NET SDK there is no
   "must pass `None` explicitly" hazard; **11 operations return `None`**, so `with_raw_response` is
   the only way to observe their status code; `prefer="return=minimal"` on **11** operations
   silently narrows the response body; and the two response modes — raising vs `ApiResult` — behave
   differently on failure.)
4. **Models** — load **python-models** the moment a request/response member isn't a plain string or
   number. (*The signature won't tell you:* `Optional[T]` here is `T | UnsetType`, **not**
   `typing.Optional` — `None` is not a legal value for it; models are frozen pydantic instances
   with `…Dict` TypedDict companions; enums are **open** (`…OrStr`), so an unknown wire value
   passes through as a plain `str` rather than raising; wire aliases differ from Python member
   names; unknown response fields are **preserved**, not dropped; and serialize via
   `to_dict`/`to_json`.)
5. **Error handling** — load **python-error-handling** before you write any `try/except`. (*The
   signature won't tell you:* there is a single `ApiError` type whose `.error` is a **per-operation
   union**, and this SDK has **four** typed arms — `Error` (orders + payments, 15 ops), `Error1`
   (vault, 6), `SubscriptionError` (subscriptions, 17), `DefaultError` (`search_balances` only) —
   plus `search_transactions`, which has **no typed arm at all**. So `isinstance(e.error, Error)`
   matches only 15 of 40 operations. Separately, **decode failures raise
   `ValidationError`/`ValueError`, not `ApiError`, and bypass both response modes**, and `httpx`
   transport exceptions reach your boundary unwrapped.)
6. **Configuration & resilience** — load **python-configuration-resilience** when you set the base
   URL, timeouts, proxies, TLS, or logging. (*The signature won't tell you:* **the SDK performs no
   retries at all** — retry/backoff is entirely yours to build or deliberately omit; `timeout` is a
   single float that maps onto `httpx`'s timeout semantics rather than bounding the whole call; and
   there is no logging hook — you wrap the transport seam. Idempotency for writes is on you, which
   is what `pay_pal_request_id` is for.)
7. **Testing** — load **python-testing** before you stub the SDK. (*The signature won't tell you:*
   the seam is the **transport protocol** (`HttpClient`/`AsyncHttpClient` in `core/transport.py`)
   passed as `custom_http_client`, or `respx` at the `httpx` layer — not the client class; assert
   on the request the SDK actually built, and cover all four failure kinds, decode failures
   included.)

## What a contract sheet must carry for this SDK

Beyond the usual signatures and model members, a Python sheet is incomplete without these, because
each one is a decision the implementer cannot make correctly from the signature alone:

1. **Sync or async** — which client class, and the reminder that the two do not mix. Plus the
   `close()`/`aclose()` obligation and where the client is held.
2. **The keyword-only boundary** for each operation: what is positional (path params, sometimes the
   body) and what sits after `*`. Every keyword-only parameter has a real default, so unlike the
   .NET SDK there is no "must pass `None` explicitly" hazard — say so, so nobody writes defensive
   `None`s.
3. **Every parameter defaulted to a real value**, because each one silently narrows a response:
   - `prefer="return=minimal"` on the **11** operations that declare it (`authorize_order`,
     `capture_order`, `confirm_order`, `create_order`, `capture_authorized_payment`,
     `reauthorize_payment`, `refund_captured_payment`, `void_payment`, `create_billing_plan`,
     `create_subscription`, `list_billing_plans`) — which is why a create response carries little
     more than id, status and links;
   - `search_transactions`: `fields="transaction_info"` (so payer, cart and shipping detail are
     **absent** unless you widen it), `balance_affecting_records_only="Y"`, `page_size=100`, `page=1`;
   - `list_billing_plans`: `page_size=10`, `page=1`, `total_required=False`;
   - `list_subscriptions`: `page_size=10`, `page=1`;
   - `list_customer_payment_tokens`: `page_size=5`, `page=1`, `total_required=False`.
4. **The 11 operations that return `None`** — `patch_order`, `update_order_tracking`,
   `delete_payment_token`, and the eight mutating `subscriptions` operations
   (`activate_billing_plan`, `deactivate_billing_plan`, `patch_billing_plan`,
   `update_billing_plan_pricing_schemes`, `activate_subscription`, `suspend_subscription`,
   `cancel_subscription`, `patch_subscription`). Their raw peer is `ApiResult[None, …]`, so
   `with_raw_response` is the only way to observe the status code.
5. **Required vs `UNSET`** for every model member the task sets, and the fact that `Optional[T]` here
   is `T | UnsetType` — **not** `typing.Optional`, so `None` is not a legal value for it.
6. **The `ApiError.error` union** for each operation in scope — there are **four** typed error
   bodies in this SDK, so the union is never uniform. Every union is `<Typed> | RawError`:

   | Typed arm | Operations | Distinguishing members |
   |---|---|---|
   | `Error` | `orders` (8) + `payments` (7) | `name`, `message`, `debug_id`, `details[ErrorDetails]`, `links` |
   | `Error1` | `vault` (6) | as `Error`, but `details[ErrorDetails1]` / `links[ErrorLinkDescription]` |
   | `SubscriptionError` | `subscriptions` (17) | adds `information_link` |
   | `DefaultError` | `search_balances` only | adds `information_link`; `details[TransactionSearchErrorDetails]` |
   | *(none)* | `search_transactions` | **no typed arm** — `ApiError.error` is always `RawError` |

   All four share required `name` / `message` / `debug_id`, so a boundary can read those uniformly —
   but `isinstance(e.error, Error)` matches only 15 of the 40 operations. And separately the **auth**
   union (`OAuthProviderError | RawError`), which is different again and reaches the same `except`
   clause.
7. **That a decode failure raises `ValidationError`/`ValueError`, not `ApiError`, in both response
   modes** — `core/raw_client.py` states this in `_build_result`'s own docstring. **And that the 2xx
   path almost never reaches it**: 16 of the 17 non-`None` return types declare no required member
   (only `SubscriptionTransactionDetails` does — `id`, `amount_with_breakdown`, `time`), so a
   truncated success body decodes cleanly with every field `UNSET` rather than raising. Any sheet row
   for a call whose result is used must name the members the implementer has to assert on.
8. **That the SDK performs no retries at all** (ADR-0001), so retry/backoff is the caller's to build
   or deliberately omit — and that `pay_pal_request_id` is the idempotency key for the writes that
   accept it.
9. **Which environment the `base_url` selects**, because omitting it is silently sandbox.
10. A **REQUIRED READING** block naming the `python-*` companions that govern the steps, with
    `MUST load` pointers.
