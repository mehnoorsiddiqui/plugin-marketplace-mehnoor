---
name: python-getting-started
description: Maxio Advanced Billing Python SDK identity and lookup layer (Python only) — install, import root, the bundled SDK map that is the locator for every contract fact, the three servers and their URL template variables, the two auth schemes, and the error model. Load this before answering any Maxio Advanced Billing Python SDK contract question and before writing any SDK code, including a change to code that already exists.
---

# Getting started with the Maxio Advanced Billing Python SDK

This is your **lookup layer**. Every contract fact — a signature, a wire name, an error arm, an enum
member — comes from the map below or from the installed package it names. Never from recall.

## SDK identity

|  |  |
|---|---|
| Display name | Maxio Advanced Billing (formerly Chargify) |
| Distribution name | `maxio-advanced-billing` |
| Import root | `maxio_advanced_billing` |
| API spec version | `1.0` |
| Requires | Python **3.10+** |
| Sync client | `MaxioAdvancedBillingClient` |
| Async client | `AsyncMaxioAdvancedBillingClient` |
| Controllers | **34** |
| Operations | **250** |
| Generator | APIMatic |

Runtime dependencies, from `pyproject.toml`: `httpx >=0.28.1,<1.0.0`, `pydantic[email] >=2.11.0,<3.0.0`,
`typing-extensions >=4.13.0,<5.0.0`. The package ships `py.typed` and is generated under strict typing,
so a type error against it is a real contract violation, not noise.

## The SDK map is the locator — do not grep

The map ships **with this skill**:

- **`sdk-map.md`** (beside this file) — client construction, the error model, the controller index, the
  model directories, and the servers/auth tables. Read it once per task.
- **`map/operations/<controller>.md`** — 34 pages, one per controller. Each block carries the operation's
  HTTP verb and route, the exact sync parsed signature, each parameter's role and wire name where it
  differs, both return types, its error case, its **Server**, and a **Type sources** table naming the
  module that declares every type it mentions.

**All `Source` paths on the map are relative to the SDK root** — the directory holding `pyproject.toml` —
never to the page carrying them. The copy bundled here sits beside this skill rather than at that root, so
resolve its `Source` paths against the SDK checkout: `maxio-sdk/` in this repository, or wherever the
project installed the distribution.

Locating something by `grep`, `glob` or `find` over the SDK tree is a defect: the map is the locator.
Open its index, follow the link, then open only the one module a row names.

## Install

Not on PyPI as published here — install from the SDK directory or its repository:

```bash
pip install ./maxio-sdk          # from a local checkout
```

Confirm it is importable **before** relying on any lookup:

```bash
python -c "import maxio_advanced_billing, pathlib; print(pathlib.Path(maxio_advanced_billing.__file__).parent)"
```

**If the package is not installed there is no source to read.** Mark the fact `UNVERIFIED`, say what
would settle it, and do not fill the hole from memory.

## Getting a client

The constructor is **keyword-only** — nothing can be passed positionally:

```python
from maxio_advanced_billing import MaxioAdvancedBillingClient
from maxio_advanced_billing.core import BasicAuthCredentials

client = MaxioAdvancedBillingClient(
    basic_auth=BasicAuthCredentials(username="...", password="..."),
    environment="us",
    server_config={"production": {"us": {"site": "your-subdomain"}}},
)
```

| Keyword | Type | Default |
|---|---|---|
| `environment` | `Literal["us", "eu", "maxio_api_gateway"]` | `"us"` |
| `timeout` | `float` | `30.0` (`DEFAULT_TIMEOUT`, `base_client.py`) |
| `server_config` | `ServerConfigOrDict \| None` | `None` |
| `custom_http_client` | `HttpClient \| None` | `None` |
| `basic_auth` | `BasicAuthCredentialsOrDict \| None` | `None` |
| `bearer_auth` | `str \| None` | `None` |

**Lifetime is yours.** The client owns an `httpx` connection pool. Call `close()` (sync) or
`await aclose()` (async), or use it as a context manager (`with` / `async with`). It must be long-lived
and app-scoped — never rebuilt per request.

**Sync and async do not mix.** `AsyncMaxioAdvancedBillingClient` is a separate class; a sync client
inside `async def` blocks the event loop, an async client in sync code is a coroutine nobody awaits.
The transport-override keyword differs too — `custom_http_client` on both, but the async one takes the
async transport protocol. Decide once, from the host application, before the first call.

## Servers and environments — the trap that costs you production

This SDK declares **three servers**, and **each operation names its own**. The operation's block on its
map page carries a **Server** bullet; it is not a client-wide choice.

| Server | Used for | `"us"` base URL |
|---|---|---|
| `production` | the main Advanced Billing API | `https://{site}.chargify.com` |
| `ebb` | Events-Based Billing ingestion | `https://events.chargify.com/{site}` |
| `oauth` | Maxio API Gateway auth | `https://{connector}.api.maxio.com` |

**The base URLs carry template variables with useless defaults.** `{site}` defaults to `"subdomain"` and
`{connector}` to `"connector"`. Omit the override and every request goes to `https://subdomain.chargify.com`
— a host that is not yours. Nothing fails at construction.

Override by passing `server_config` a dict nested exactly as the table reads:

```python
server_config={
    "production": {"us": {"site": "acme"}},
    "ebb":        {"us": {"site": "acme"}},
}
```

`base_url` is overridable at the same level — `{"production": {"us": {"base_url": "http://localhost:9999"}}}` —
which is how you point the SDK at a mock or a local stand-in.

**Environments** (`server/environment.py`): `Environment: TypeAlias = Literal["us", "eu", "maxio_api_gateway"]`.
`"eu"` is only for accounts provisioned for EU hosting; `"maxio_api_gateway"` authenticates with a
connector Bearer token instead of Basic — **and EBB ingestion still uses its direct URL**, so a gateway
setup does not move every call.

## Auth — two schemes, both optional

| Scheme | Pass |
|---|---|
| Basic | `basic_auth={"username": ..., "password": ...}` or `BasicAuthCredentials(...)` |
| Bearer | `bearer_auth="<token>"` |

For a site API key, the key is the **username** and the password is the literal `"x"` — that is Maxio's
convention, not a placeholder.

**Both default to `None`, which installs `no_auth`.** Omit them and requests go out unauthenticated and
come back `401`, with **no failure at construction**. Nothing validates that you supplied credentials.
Validate at startup yourself — see `python-authentication`.

Which scheme an operation uses is the operation's business; the map block names its server, and the
gateway environment is the case that swaps Basic for Bearer.

## The error model — read once, applies to every operation

Every operation exists in two response modes:

- **Parsed call** — returns the decoded payload, raises `ApiError` on an error status. `.error` holds the
  decoded body, `.status_code` the status, `.response` the `HttpResponse`.
- **Raw call** — reached through `.with_raw_response`; returns `ApiResult` (`Success` | `Failure`) and
  **never raises for an API error**. Read `.payload` on `Success`, `.error` on `Failure`.

What `.error` holds is fixed per operation:

| Case | Meaning | Count |
|---|---|---|
| **A — typed** | the operation documents error statuses; `errors/` declares a union alias over them, with `RawError` always the last arm | **166** |
| **B — raw** | no documented error status; `.error` is `RawError` | **84** |

```python
from maxio_advanced_billing.core import ApiError, RawError

try:
    subscription = client.subscriptions.read_subscription(sub_id)
except ApiError as e:
    if isinstance(e.error, RawError):
        log.warning("undocumented status %s", e.status_code)
    else:
        ...  # narrow the typed arms with isinstance
```

**A decode failure is not an `ApiError`.** It surfaces as `pydantic.ValidationError` / `ValueError` and
**bypasses both response modes**. `httpx` transport exceptions arrive unwrapped. An exception that does
not look like an API error may still be one of this SDK's failure kinds — see `python-error-handling`.

**29 of the 250 operations return `None`** (deletes, archives, `send_invoice`). For those,
`.with_raw_response` is the only way to observe the status code.

## Models

| Group | Count | Directory |
|---|---|---|
| Models (`SdkBaseModel`) | 563 | `models/` |
| Enums | 98 | `models/enums/` |
| Discriminated unions | 7 | `models/unions/` |
| Plain unions | 83 | `models/unions/` |
| Error aliases | 166 | `errors/` |

Module name is the type name in snake_case (`AchAgreement` ↔ `ach_agreement.py`); an error alias drops
its `Body` suffix (`ActivateSubscriptionErrorBody` ↔ `activate_subscription_error.py`). Take the module
from the operation's **Type sources** table; never grep for a type.

- A field whose wire name differs carries `Field(alias=...)` — read the alias off the field, never derive it.
- `Optional[T]` (from `core`, **not** `typing.Optional`) defaults to `UNSET`; `OptionalNullable[T]` may also
  be explicitly null. Passing `None` to the first is a type error, not a value that serializes.
- Every model, enum and union has an **input companion** exported beside it — `AchAgreement` ↔ `AchAgreementDict`,
  and enums as `<Name>OrStr` / `<Name>OrInt`. Where a signature names the companion you may pass the model or a
  plain dict. The `OrStr` form additionally accepts a wire value this SDK version does not know.

## Contract facts — the runtime modules

For behaviour the map does not cover, read the one module that owns it, under the installed package:

| Question | Module |
|---|---|
| An operation's real signature and return type | `apis/<controller>.py` |
| Client construction, controller wiring | `client.py`, `async_client.py`, `base_client.py` |
| Timeout default (`DEFAULT_TIMEOUT = 30.0`) | `base_client.py` |
| Request/response pipeline, 2xx-vs-error split | `core/raw_client.py` |
| `ApiError` shape | `core/exceptions.py` |
| `Success` / `Failure` / `RawError` | `core/results.py` |
| Per-call overrides (`timeout`, `extra_headers`) | `core/request_options.py` |
| `UNSET`, `Optional`, `OptionalNullable` | `core/optionality.py` |
| Model base config, `to_dict` / `to_json` | `core/models.py` |
| Transport protocols (the test seam) | `core/transport.py` |
| httpx adapter | `core/httpx_transport.py` |
| Auth schemes | `auth.py`, `core/auth/` |
| Base-URL and template resolution | `server/server_config.py`, `server/environment.py` |

**Read scoped.** These modules carry long design docstrings. `grep -n` for the symbol inside the named
module and read the surrounding lines — never a whole file, and never copy a docstring's rationale onto a
contract sheet.

## No retries, no paginator — both are yours to build

- **The SDK performs no retries at all.** `core/raw_client.py` cites ADR-0001 ruling them out. Anything the
  task needs there you write yourself, or deliberately omit and say so. → `python-configuration-resilience`
- **There is no pagination helper.** `page` / `per_page` are ordinary parameters with real defaults
  (`page=1`, `per_page=50`). A loop over pages is hand-written, and needs a cap that does not depend on the
  provider's cooperation.

## Integration workflow — load the companion at each step

Load the named skill **before** writing that step's code, even if you have already read the module.

1. **Planning the whole integration** — **`python-integration-planning`**, before any architecture decision.
2. **Client construction and lifetime** — `python-client-initialization`. (*Keyword-only; you own `close()`/`aclose()`; app-scoped, not per-request; sync and async never mix.*)
3. **Authentication** — `python-authentication`. (*Both schemes optional and silently absent; `{site}`/`{connector}` unset sends you to the wrong host.*)
4. **Calling an endpoint** — `python-calling-endpoints`. (*Everything after `*` is keyword-only with real defaults; 29 operations return `None`; each operation names its own server.*)
5. **Building payloads / reading responses** — `python-models`. (*`UNSET` vs `None`; `Field(alias=...)`; input companions; open enums.*)
6. **Error boundary** — `python-error-handling`. (*Case A vs Case B; decode failures bypass both modes; transport exceptions arrive unwrapped.*)
7. **Timeouts, retries, paging** — `python-configuration-resilience`. (*No retries and no paginator exist — you build both.*)
8. **Callbacks, replays, unanswered writes** — `python-inbound-state`. (*Maxio sends webhooks; `client.webhooks` has 6 operations.*)
9. **Pinning and change safety** — `python-sdk-drift`, before shipping and again on any regeneration.
10. **Proving it works** — `python-testing`. (*Override `base_url` via `server_config` to reach a local stand-in.*)

## What a contract sheet must carry for this SDK

For every in-scope operation, collected in **one** pass:

- exact method name, its controller attribute, and the **keyword-only boundary**;
- the HTTP verb and route (from the map — never reconstructed from the method name);
- **which server it uses**, and the template variables that server needs;
- required parameters vs those with defaults, with wire names where they differ;
- the return type — and whether it is `None`;
- the error case: **A** (the alias and the status each arm maps from) or **B** (`RawError`);
- for each payload model touched: the module, required-vs-`UNSET` members, and wire aliases;
- enum members and wire values for anything you branch on.

Recurse into a model's members only where the task actually sets them — a full transitive expansion of a
Maxio model is hundreds of rows and nobody needs it.
