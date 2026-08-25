---
name: python-getting-started
description: PayPal Python SDK identity and lookup layer for the paypal-python-sdk helper agent (Python only) — install, import root, base URL/environments, the auth pattern. The helper agent loads this to answer contract questions; other agents work from the contract sheet it produces.
---

# Getting started with the PayPal Python SDK

> **Who this skill is for.** This is the **lookup layer**, preloaded for the `paypal-python-sdk` helper
> agent — if you are it, this skill is yours to follow directly and fully. An implementer works from
> the contract sheet this agent produces, and asks the warm agent for any fact the sheet is missing. If you are the
> main agent, you should not be reading this — load `python-integrate-paypal` instead.

## SDK identity

Verified against `pay_pal_server_sdk/` and `pyproject.toml` of the generated package at version
`2.29`. **Re-verify after a version bump** — this page is a snapshot, not a live read.

| Fact | Value |
|---|---|
| Distribution name (what you install) | `pay-pal-server-sdk` |
| Import root (what you import) | `pay_pal_server_sdk` — note the underscores; the two names differ |
| Version | `2.29` |
| Python floor | **`>=3.10`** (classifiers list 3.10–3.14) |
| Runtime dependencies | `httpx>=0.28.1,<1.0.0` · `pydantic[email]>=2.11.0,<3.0.0` · `typing-extensions>=4.13.0,<5.0.0` |
| Typing | ships `py.typed`; the package is checked under `mypy --strict` with `warn_unreachable`. Callers get full inference — **a type error against this SDK is a real contract violation, not noise** |
| Line length / lint | `ruff`, 120 cols (only relevant when editing the SDK itself) |

Install with whatever the project already uses:

```bash
uv add pay-pal-server-sdk         # uv
poetry add pay-pal-server-sdk     # poetry
pip install pay-pal-server-sdk    # pip
```

### What the root package exports

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

Everything else is imported from its own subpackage — and the split matters, because the three
places a caller reaches for are different modules:

| What you need | Where it lives |
|---|---|
| Domain models, their `…Dict` companions | `pay_pal_server_sdk.models` |
| Enums (and their open `…OrStr` aliases) | `pay_pal_server_sdk.models.enums` |
| `ApiError`, `RawError`, `Success`, `Failure`, `ApiResult`, `RequestOptions`, `ClientCredentials`, `HttpClient`/`AsyncHttpClient`, `HttpxClient`, `OAuthProviderError`, `SdkBaseModel`, `UNSET` | `pay_pal_server_sdk.core` |
| Per-operation error *unions* (`CreateOrderErrorBody`, …) | `pay_pal_server_sdk.errors` |

`pay_pal_server_sdk.core` re-exports its whole public surface (a curated `__all__`), so import from
`…core` rather than from the private modules beneath it (`…core.results`, `…core.exceptions`).

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
`401`. Nothing fails at construction. See `python-authentication` for the full picture, including
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

Every controller has an `Async…` peer whose operations are
identical in name and parameters and differ solely by being awaited. Do not emit a separate row for
an async operation; state the rule once on the sheet.

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
| Client construction, keywords, controller wiring | `pay_pal_server_sdk/client.py`, `async_client.py`, `base_client.py` |
| Timeout default and validation | `base_client.py` (`DEFAULT_TIMEOUT = 30.0`) |
| The request/response pipeline, 401 handling, 2xx-vs-error split | `core/raw_client.py` |
| Exception shape | `core/exceptions.py` |
| `Success`/`Failure`/`RawError` | `core/results.py` |
| Per-call overrides | `core/request_options.py` |
| `UNSET`, `Optional`, `OptionalNullable`, `strip_unset` | `core/optionality.py` |
| Model base config, `to_dict`/`to_json` | `core/models.py` |
| Open-enum coercion | `core/converters/open_enum.py` |
| Transport protocols (the test seam) | `core/transport.py` |
| httpx adapter, proxy/TLS knobs | `core/httpx_transport.py` |
| Token fetch, credential placement | `core/auth/schemes/oauth2_client_credentials.py`, `core/auth/models.py` |
| An operation's error mapper (status → schema) | `errors/<operation>_error.py` |

**Read scoped.** These modules carry long design docstrings; `grep -n` for the symbol and read the
surrounding lines rather than whole files. Never quote a docstring's design rationale onto a contract
sheet — the sheet carries facts an implementer must obey, not the reasoning behind them.

## What a contract sheet must carry for this SDK

Beyond the usual signatures and model members, a Python sheet is incomplete without these, because
each one is a decision the implementer cannot make correctly from the signature alone:

1. **Sync or async** — which client class, and the reminder that the two do not mix.
2. **The keyword-only boundary** for each operation: what is positional (path params, sometimes the
   body) and what sits after `*`. Every keyword-only parameter has a real default, so unlike the
   .NET SDK there is no "must pass `None` explicitly" hazard — say so, so nobody writes defensive
   `None`s.
3. **Every parameter defaulted to a real value**, because each one silently narrows a response:
   - `prefer="return=minimal"` on the **11** operations that declare it — which is why a create
     response carries little more than id, status and links;
   - `search_transactions`: `fields="transaction_info"` (so payer, cart and shipping detail are
     **absent** unless you widen it), `balance_affecting_records_only="Y"`, `page_size=100`, `page=1`;
   - `list_billing_plans` / `list_subscriptions`: `page_size=10`, `page=1` (plus
     `total_required=False` on the first);
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
   bodies in this SDK, so the union is never uniform:

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
   or deliberately omit.
9. A **REQUIRED READING** block naming the `python-*` companions that govern the steps, with
   `MUST load` pointers.
