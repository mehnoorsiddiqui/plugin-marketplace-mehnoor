<!-- Generated file — do not edit; regenerated with the SDK. -->

# SDK map — Maxio Advanced Billing (Python)

> A generated table of contents for this SDK. Consult this map and its sub-pages to learn signatures, error types, and server/auth wiring **by lookup**. Model shapes and enum values are *not* duplicated here — the map names the module declaring each type; read the shape there. Every name is the emitted spelling, so a wrong one fails at import rather than working silently.

|  |  |
| --- | --- |
| SDK display name | Maxio Advanced Billing |
| Root package | `maxio_advanced_billing` |
| Distribution name | `maxio-advanced-billing` |
| Requires | Python 3.10 or later |
| API spec version | `1.0` |
| Generator | APIMatic |

Staleness check: the API spec version above changes when the SDK is regenerated from a new spec, and the package version is what `pip show` reports for the installed SDK. If a lookup here fails at import, re-read the module named in the row.

All `Source` paths on this map and its sub-pages are relative to the **SDK root** — the directory holding this file and `pyproject.toml` — never to the page that carries them. Open them as-is from the SDK root; if the SDK sits under a subdirectory of a larger repo, prefix that subdirectory.

---

## Getting a client

### Synchronous client

```python
from maxio_advanced_billing import MaxioAdvancedBillingClient
from maxio_advanced_billing.core import BasicAuthCredentials

client = MaxioAdvancedBillingClient(
    basic_auth=BasicAuthCredentials(username="YOUR_USERNAME", password="YOUR_PASSWORD"),
    bearer_auth="YOUR_BEARER_TOKEN",
    environment="us",
)

# TODO: call endpoints here -- see api-reference.md

client.close()
```

Alternatively, scope it — `with MaxioAdvancedBillingClient(...) as client:` closes the pool on exit.

### Asynchronous client

```python
from asyncio import run

from maxio_advanced_billing import AsyncMaxioAdvancedBillingClient
from maxio_advanced_billing.core import BasicAuthCredentials


async def main() -> None:
    client = AsyncMaxioAdvancedBillingClient(
        basic_auth=BasicAuthCredentials(username="YOUR_USERNAME", password="YOUR_PASSWORD"),
        bearer_auth="YOUR_BEARER_TOKEN",
        environment="us",
    )
    # TODO: call endpoints here, awaiting each -- see api-reference.md
    await client.aclose()


run(main())
```

Alternatively, scope it — `async with AsyncMaxioAdvancedBillingClient(...) as client:` closes the pool on exit.

`AsyncClient` (`maxio_advanced_billing/async_client.py`) mirrors `Client` method for method, each endpoint method a coroutine. It takes the same keywords, except that each client accepts only its own transport and — where the **Async Type** column differs — only its own flavor.

`Client` and `AsyncClient` are aliases of `MaxioAdvancedBillingClient` and `AsyncMaxioAdvancedBillingClient` — the names tracebacks and `repr()` show; all four import from the root.

`close()` / `aclose()` closes the transport even when you supplied one via `custom_http_client=` / `custom_async_http_client=`, and a closed client cannot be reused.

Every API group is a property on the client (e.g. `client.api_exports`). Every constructor argument is optional and keyword-only. Sources: `maxio_advanced_billing/client.py`, `maxio_advanced_billing/async_client.py`:

| Keyword | Sync Type | Async Type | Default |
| --- | --- | --- | --- |
| `environment` | `Environment` | `Environment` | `"us"` |
| `timeout` | `float` | `float` | `30.0` seconds |
| `server_config` | `ServerConfigOrDict \| None` | `ServerConfigOrDict \| None` | `None` |
| `custom_http_client` | `HttpClient \| None` | — | `None` |
| `custom_async_http_client` | — | `AsyncHttpClient \| None` | `None` |
| `basic_auth` | `BasicAuthCredentialsOrDict \| None` | `BasicAuthCredentialsOrDict \| None` | `None` |
| `bearer_auth` | `str \| None` | `str \| None` | `None` |

The types those columns name — where each imports from and, for a credentials dict, its keys:

| Type | Import from | Shape |
| --- | --- | --- |
| `Environment` | `maxio_advanced_billing.server` | `Literal` of the Environments table's names |
| `ServerConfigOrDict` | `maxio_advanced_billing.server` | keys as the Servers & auth tables read |
| `HttpClient` | `maxio_advanced_billing.core` | protocol — `send(request: HttpRequest) -> HttpResponse` · `close()` |
| `BasicAuthCredentialsOrDict` | `maxio_advanced_billing.core` | `BasicAuthCredentials` or a dict: `username: str` · `password: str` |
| `AsyncHttpClient` | `maxio_advanced_billing.core` | protocol — `async send(request: HttpRequest) -> HttpResponse` · `async aclose()` |

---

## Error-handling model (read once — applies to every operation)

Every operation is reached in two response modes:

- **Parsed call.** Returns the decoded payload and raises `ApiError` on an error status, with the decoded body on `.error` and the status on `.status_code`.
- **Raw call.** Reached through `.with_raw_response`; returns `ApiResult` — `Success` or `Failure` — and never raises for an API error. Read `.payload` on a `Success` or `.error` on a `Failure`; both carry `.response`.

What `.error` holds is fixed per operation. There are two cases:

- **Case A — typed error.** The operation documents at least one error status, so `maxio_advanced_billing/errors/` declares a union alias over the bodies those statuses map to — `RawError` is always its last arm, for any undocumented status — and `.error` is annotated with that alias. Narrow it with `isinstance`. The operation blocks name the alias and the status each arm maps from.
- **Case B — raw error.** The operation documents no error status; `.error` is `RawError` (`maxio_advanced_billing/core/results.py`): `status_code: int` · `content: bytes` · `text(encoding="utf-8"): str` · `json(): Any` · `response: HttpResponse`.

Core runtime types (`maxio_advanced_billing/core/`) — public members with their **declared types**, verbatim from source:

| Type | Public members | Source |
| --- | --- | --- |
| `ApiError` — raised by every parsed call; `.error` is a Case A alias from `maxio_advanced_billing/errors/` or `RawError` | `error: E` · `status_code: int` · `response: HttpResponse` | `maxio_advanced_billing/core/exceptions.py` |
| `ApiResult[T, E]` — returned by every raw call; the `Success[T] \| Failure[E]` union | `payload: T` (on `Success`) · `error: E` (on `Failure`) · `response: HttpResponse` (on both) | `maxio_advanced_billing/core/results.py` |
| `RawError` | `status_code: int` · `content: bytes` · `text(encoding="utf-8"): str` · `json(): Any` · `response: HttpResponse` | `maxio_advanced_billing/core/results.py` |

Typed error bodies (the arms of a Case A alias) are ordinary models — no special handling. The operation's **Type sources** table gives the module that declares each one; read field names, declared types and JSON aliases there, as for any other model.

```python
from maxio_advanced_billing.core import ApiError, RawError
from maxio_advanced_billing.models import SingleErrorResponse1

try:
    response = client.api_exports.export_invoices()
except ApiError as e:
    # Case A — typed error: e.error is ExportInvoicesErrorBody
    if isinstance(e.error, SingleErrorResponse1):
        # Handle 409
        print(e.error)
    if isinstance(e.error, RawError):
        # Any other error status
        print(e.status_code, e.error.text())
```

**Raw (`.with_raw_response`) variants: present on every operation** — the same call returns `ApiResult` instead of raising, with the same body on `Failure.error`. Of **250 operations**, **166 are Case A (typed)** and **84 are Case B (raw)**.

---

## Operations — by controller (34 pages, 250 operations)

Each links to a sub-page with one block per operation, headed by its full accessor path: the HTTP verb and route (for a mock, a raw request or a provider-side log — never reconstruct it from the method name), the sync parsed signature with its required positional parameters, each parameter's role and — where it differs — wire name, both return types, and its error case — **Case A** names the alias and the status each arm maps from, **Case B** names `RawError`. Every block also carries a **Type sources** table — every type it names, with the module that declares it.

**Each block states what is specific to its operation. Everything below holds for every operation, and blocks never restate it — silence means the default applies.**

| Applies to every operation | Stated where |
| --- | --- |
| **Four spellings, one signature** — the same method name and parameters on `Client` and `AsyncClient`, each also reachable through `.with_raw_response`; the async twin is a coroutine to `await`, with the same return types and error case, and where the **Async Type** column differs, pass the type it names | Getting a client |
| **Parsed raises, raw returns** — `ApiError` versus `ApiResult` | Error-handling model |
| **Case B error is always `RawError`** — also the last arm of every Case A alias, where a block's **Error arms** bullet ends in it | Error-handling model |
| **A trailing `request_options`** — keyword-only and optional, for per-call overrides such as a timeout or extra headers; every signature ends with it | here (`maxio_advanced_billing/core/request_options.py`) |
| **Each operation names its own server** — this SDK declares several, so every block carries a **Server** bullet with the server's key in `server_config=` | its block |
| **Parameter names are literal** — signatures are generated code verbatim, and everything behind the bare `*` must be passed by name | here |
| **A parameter's wire name is its Python name** — sent as-is on the path, query string, header or body, unless the block's **Params** bullet carries a wire name beside the role | here |

**The operation's behavioural prose lives on the operation itself**, as the method's docstring in the module named at the top of its page, and again in `api-reference.md` with a per-parameter description and a usage sample. Blocks here give you the contract — names, types, shapes, errors. Where an operation's *semantics* decide what you must pass, that is what the docstring settles; read it there rather than filling it in from memory.

Sub-pages chunk per `###` block: each block is self-contained given the table above, and assumes this page is loaded beside it.

| Controller | Ops | Page |
| --- | --- | --- |
| `client.api_exports` | 9 | [map/operations/api_exports.md](map/operations/api_exports.md) |
| `client.advance_invoice` | 3 | [map/operations/advance_invoice.md](map/operations/advance_invoice.md) |
| `client.billing_portal` | 4 | [map/operations/billing_portal.md](map/operations/billing_portal.md) |
| `client.component_price_points` | 12 | [map/operations/component_price_points.md](map/operations/component_price_points.md) |
| `client.components` | 12 | [map/operations/components.md](map/operations/components.md) |
| `client.coupons` | 14 | [map/operations/coupons.md](map/operations/coupons.md) |
| `client.custom_fields` | 9 | [map/operations/custom_fields.md](map/operations/custom_fields.md) |
| `client.customers` | 7 | [map/operations/customers.md](map/operations/customers.md) |
| `client.events` | 3 | [map/operations/events.md](map/operations/events.md) |
| `client.events_based_billing_segments` | 6 | [map/operations/events_based_billing_segments.md](map/operations/events_based_billing_segments.md) |
| `client.insights` | 4 | [map/operations/insights.md](map/operations/insights.md) |
| `client.invoices` | 19 | [map/operations/invoices.md](map/operations/invoices.md) |
| `client.maxio_gateway` | 1 | [map/operations/maxio_gateway.md](map/operations/maxio_gateway.md) |
| `client.offers` | 5 | [map/operations/offers.md](map/operations/offers.md) |
| `client.payment_profiles` | 12 | [map/operations/payment_profiles.md](map/operations/payment_profiles.md) |
| `client.product_families` | 4 | [map/operations/product_families.md](map/operations/product_families.md) |
| `client.product_price_points` | 11 | [map/operations/product_price_points.md](map/operations/product_price_points.md) |
| `client.products` | 6 | [map/operations/products.md](map/operations/products.md) |
| `client.proforma_invoices` | 10 | [map/operations/proforma_invoices.md](map/operations/proforma_invoices.md) |
| `client.reason_codes` | 5 | [map/operations/reason_codes.md](map/operations/reason_codes.md) |
| `client.referral_codes` | 1 | [map/operations/referral_codes.md](map/operations/referral_codes.md) |
| `client.sales_commissions` | 3 | [map/operations/sales_commissions.md](map/operations/sales_commissions.md) |
| `client.sites` | 3 | [map/operations/sites.md](map/operations/sites.md) |
| `client.subscription_components` | 17 | [map/operations/subscription_components.md](map/operations/subscription_components.md) |
| `client.subscription_group_invoice_account` | 4 | [map/operations/subscription_group_invoice_account.md](map/operations/subscription_group_invoice_account.md) |
| `client.subscription_group_status` | 4 | [map/operations/subscription_group_status.md](map/operations/subscription_group_status.md) |
| `client.subscription_groups` | 9 | [map/operations/subscription_groups.md](map/operations/subscription_groups.md) |
| `client.subscription_invoice_account` | 7 | [map/operations/subscription_invoice_account.md](map/operations/subscription_invoice_account.md) |
| `client.subscription_notes` | 5 | [map/operations/subscription_notes.md](map/operations/subscription_notes.md) |
| `client.subscription_products` | 2 | [map/operations/subscription_products.md](map/operations/subscription_products.md) |
| `client.subscription_renewals` | 11 | [map/operations/subscription_renewals.md](map/operations/subscription_renewals.md) |
| `client.subscription_status` | 10 | [map/operations/subscription_status.md](map/operations/subscription_status.md) |
| `client.subscriptions` | 12 | [map/operations/subscriptions.md](map/operations/subscriptions.md) |
| `client.webhooks` | 6 | [map/operations/webhooks.md](map/operations/webhooks.md) |

---

## Models — where they live, how to build them

**Shapes live only in the source.** Every module under `maxio_advanced_billing/models/` declares one type plus its input companion, and every module under `maxio_advanced_billing/errors/` one alias plus the mapper that builds it; no two share a name. Take a type's module from the operation's **Type sources** table. When no retrieved chunk names it, the module is the type name in snake_case under the kind's directory below (`AchAgreement` ↔ `ach_agreement.py`; an error alias drops its `Body` suffix: `ActivateSubscriptionErrorBody` ↔ `activate_subscription_error.py`). Never grep for a type.

| Group | Count | Directory (module = `<type_name>.py`) |
| --- | --- | --- |
| Models (`SdkBaseModel` pydantic classes) | 563 | `maxio_advanced_billing/models/` |
| Enums (`Enum` over `str` / `int`) — Python member names + wire values | 98 | `maxio_advanced_billing/models/enums/` |
| Unions (discriminated) — `TypeAlias` over the arms, tagged via `Field(discriminator=…)` | 7 | `maxio_advanced_billing/models/unions/` |
| Unions (plain) — `TypeAlias` over the arms | 83 | `maxio_advanced_billing/models/unions/` |
| Error aliases (one per Case A operation) | 166 | `maxio_advanced_billing/errors/` |

Conventions: a model is a `SdkBaseModel` (pydantic) class; a field whose wire name differs from its Python name carries it as `Field(alias=…)` (`type_` ↔ `"type"`) — read the alias off the field rather than deriving it. An omittable field is annotated `Optional[T]` and defaults to `UNSET`, and one that may also be explicitly null is `OptionalNullable[T]`; both come from `core` and neither is `typing.Optional` — there is no `None` arm unless the spec declared the property nullable, so passing `None` to the first is a type error rather than a value that serializes.

Every model, enum and union also has an **input companion**, exported beside it from the same package (`AchAgreement` ↔ `AchAgreementDict`). Wherever a signature names the companion you may pass either the model instance or a plain dict with the same keys, whichever reads better at the call site. An enum is a real `Enum` subclass over `str` / `int`; its companion is spelled `<Name>OrStr` or `<Name>OrInt` (`AllVaults` ↔ `AllVaultsOrStr`) and additionally accepts a wire value this SDK version does not know. A union is a `TypeAlias` over its arms — a discriminated one carries `Field(discriminator=…)`, so build the arm you mean and the tag is written for you.

Import paths by content type (`from <package> import <Name>`):

| Contents | Import from |
| --- | --- |
| Client (root) | `maxio_advanced_billing` |
| Operation controllers | `maxio_advanced_billing.apis` |
| Models | `maxio_advanced_billing.models` |
| Enums | `maxio_advanced_billing.models.enums` |
| Unions | `maxio_advanced_billing.models.unions`, `maxio_advanced_billing.models` |
| Error aliases | `maxio_advanced_billing.errors` |
| Core runtime (`ApiError`, `ApiResult`, `RawError`, …) | `maxio_advanced_billing.core` |

---

## Servers & auth

**Basic auth.** Pass `basic_auth={"username": …, "password": …}`, or a `BasicAuthCredentials`.

**Bearer token.** Pass `bearer_auth="<token>"`.

**Environments.** `environment=` selects the target environment (`maxio_advanced_billing/server/environment.py`):

| Environment | Hosting |
| --- | --- |
| `"us"` *(default)* | Default Advanced Billing environment hosted in US. Valid for the majority of our customers. |
| `"eu"` | Advanced Billing environment hosted in EU. Use only when you requested EU hosting for your AB account. |
| `"maxio_api_gateway"` | Access Advanced Billing through a Maxio API Gateway connector. Authenticate with your connector Bearer token instead of Basic auth. Events-Based Billing ingestion does not go through the gateway and keeps its direct URL. |

**3 servers.** Base-URL templates and override points (`maxio_advanced_billing/server/server_config.py`):

| Server | `"us"` base URL | `"eu"` base URL | `"maxio_api_gateway"` base URL | Override point |
| --- | --- | --- | --- | --- |
| `production` | `https://{site}.chargify.com` | `https://{site}.ebilling.maxio.com` | `https://{connector}.api.maxio.com/api/v1/billing` | `{"production": {"us": {"base_url": …}}}` (and the other environments) |
| `ebb` | `https://events.chargify.com/{site}` | `https://events.chargify.com/{site}` | `https://events.chargify.com/{site}` | `{"ebb": {"us": {"base_url": …}}}` (and the other environments) |
| `oauth` | `https://{connector}.api.maxio.com` | `https://{connector}.api.maxio.com` | `https://{connector}.api.maxio.com` | `{"oauth": {"us": {"base_url": …}}}` (and the other environments) |

`production` · `"us"` template variables: `{site}` defaults to `"subdomain"` — override `{"production": {"us": {"site": …}}}`.

`production` · `"eu"` template variables: `{site}` defaults to `"subdomain"` — override `{"production": {"eu": {"site": …}}}`.

`production` · `"maxio_api_gateway"` template variables: `{connector}` defaults to `"connector"` — override `{"production": {"maxio_api_gateway": {"connector": …}}}`.

`ebb` · `"us"` template variables: `{site}` defaults to `"subdomain"` — override `{"ebb": {"us": {"site": …}}}`.

`ebb` · `"eu"` template variables: `{site}` defaults to `"subdomain"` — override `{"ebb": {"eu": {"site": …}}}`.

`ebb` · `"maxio_api_gateway"` template variables: `{site}` defaults to `"subdomain"` — override `{"ebb": {"maxio_api_gateway": {"site": …}}}`.

`oauth` · `"us"` template variables: `{connector}` defaults to `"connector"` — override `{"oauth": {"us": {"connector": …}}}`.

`oauth` · `"eu"` template variables: `{connector}` defaults to `"connector"` — override `{"oauth": {"eu": {"connector": …}}}`.

`oauth` · `"maxio_api_gateway"` template variables: `{connector}` defaults to `"connector"` — override `{"oauth": {"maxio_api_gateway": {"connector": …}}}`.

Pick a row with `environment=`, and override any of these by passing `server_config=` a dict nested exactly as the columns above read — `{"production": {"us": {"base_url": …}}}` — with each row's variables sitting beside its `base_url`.

