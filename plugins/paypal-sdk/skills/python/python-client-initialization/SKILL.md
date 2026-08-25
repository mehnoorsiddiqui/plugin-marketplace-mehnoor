---
name: python-client-initialization
description: Creating and holding an APIMatic-generated Python SDK client — the keyword-only constructor, choosing the sync or async class, transport ownership and the close/aclose obligation, and where the client should live in a script, a FastAPI/Django app, or a worker. Load before wiring the client into an application, or when deciding sync vs async.
---

# Initializing an APIMatic Python SDK client

This applies to **any** APIMatic-generated Python SDK. Replace placeholders with the real names from
the SDK you are using:

- `{root_package}` — the import root (e.g. `pay_pal_server_sdk`). **This is not the pip name**; the
  distribution is usually hyphenated where the package is underscored.
- `{Api}Client` / `Async{Api}Client` — the two client classes. Most SDKs also export short aliases
  `Client` and `AsyncClient`.

## Two clients, chosen once

An APIMatic Python SDK ships **two complete client classes**, sync and async. They are peers: same
controllers, same operation names, same parameters. The only differences are `await` and the
transport underneath.

```python
from {root_package} import Client, AsyncClient
```

**Pick one from the host application, not from preference, and pick it before the first call.** There
is no bridge between them:

- A sync client called from `async def` performs blocking I/O on the event loop. It works in testing
  and starves every other coroutine under load — the failure appears as unrelated latency elsewhere.
- An async client used from sync code returns a coroutine nobody awaits. That is a `RuntimeWarning`
  and a silently skipped API call, not an error.

Rules of thumb: FastAPI / Starlette / aiohttp / async worker → async client. Django (unless fully
ASGI), Flask, Celery, a CLI, a script, a notebook → sync client. Mixed codebase → the client belongs
to whichever layer actually issues the call, and if both do, construct one of each rather than
bridging with `asyncio.run` inside a request handler.

## The constructor is keyword-only

```python
client = {Api}Client(
    base_url=...,             # str | None      — None means the SDK's default environment
    timeout=...,              # float seconds   — applies per request
    custom_http_client=...,   # HttpClient | None      — your own transport
    oauth2=...,               # credentials — see python-authentication
    oauth2_token_source=...,  # override token acquisition
)
```

Every parameter is after `*` — there are **no positional arguments at all**. A positional call is an
immediate `TypeError`, which is the intended design: it keeps call sites readable and lets the
generator add options without breaking anyone.

The keyword names are per-SDK — one per security scheme the API declares, plus the four above. For
the PayPal SDK the scheme keywords are exactly `oauth2` and `oauth2_token_source`.

The async class takes the same set with one rename: `custom_async_http_client` instead of
`custom_http_client`. Passing a sync transport to the async client (or the reverse) is a type error
the checker catches, because the two transport protocols are distinct types. The token-source
keyword is likewise flavour-specific (`TokenSource` vs `AsyncTokenSource`), so a sync token source
handed to the async client is caught too.

**Validation happens at construction, not at first call.** A non-positive `timeout` raises
`ValueError` immediately; credentials supplied in dict form are validated by pydantic with
`extra="forbid"`, so a misspelled key raises `ValidationError` there and then. Getting a
`ValidationError` out of a constructor is normal here — read it, don't defend against it.

Two things it does **not** validate, so do not read a clean construction as a working client:

- **`base_url` is taken as a string, unchecked** beyond `ServerConfig`'s `extra="forbid"`. A typo or
  the wrong environment surfaces as a connection error or a `401`, never at construction.
- **The credentials are never exercised.** The token is fetched lazily on the first call
  (`python-authentication`), so wrong credentials construct perfectly happily.

## The client owns a connection pool — close it

The client builds a pooled HTTP transport (httpx) unless you supply one. **That pool is a resource
with a lifetime**, and this is the obligation most integrations miss, because leaking it produces no
error — only `ResourceWarning`s in test output, sockets that accumulate in long-running processes,
and a warning at interpreter shutdown.

Use the context manager whenever the client's life matches a scope:

```python
with {Api}Client(...) as client:            # sync: calls close() on exit
    ...

async with Async{Api}Client(...) as client:  # async: calls aclose() on exit
    ...
```

When the client outlives any single scope (the normal case for a server), construct it once at
startup and close it at shutdown — `client.close()` for sync, `await client.aclose()` for async. Note
the asymmetry: the async method is `aclose`, matching httpx, and calling `close()` on an async client
is an `AttributeError`.

## One client, long-lived — never per request

The client is designed to be constructed once and reused for the process's life. Two reasons, and the
second is the one that bites:

1. Controllers are `cached_property`, so `client.orders` is built once and reused — cheap.
2. **A client per request means a connection pool per request.** Every call then pays a fresh TCP and
   TLS handshake, and — in an SDK with managed OAuth — **re-fetches the access token**, because the
   token cache lives on the client's auth scheme. That is two extra round trips per call, and it
   quietly multiplies your token-endpoint traffic by your request rate, which providers rate-limit.

```python
# module scope, or an app-level singleton
client = {Api}Client(oauth2=..., timeout=10.0)
```

### FastAPI / Starlette

Build it in the lifespan handler and hand it out via dependency injection or `app.state`:

```python
from contextlib import asynccontextmanager

@asynccontextmanager
async def lifespan(app):
    app.state.paypal = Async{Api}Client(oauth2=..., timeout=10.0)
    try:
        yield
    finally:
        await app.state.paypal.aclose()

app = FastAPI(lifespan=lifespan)

async def get_client(request: Request) -> Async{Api}Client:
    return request.app.state.paypal          # inject with Depends(get_client)
```

Do not build the client in a `@app.on_event("startup")` handler without a matching shutdown, and do
not build it inside a route.

### Django

A module-level client in an app module (or a lazily-initialised module global) is the pragmatic
placement; close it from an `AppConfig.ready()`-registered `atexit` hook if the deployment recycles
workers rather than killing them. Under Gunicorn/uWSGI with forking workers, construct the client
**after** the fork — a pool inherited across `fork()` is shared by processes that each think they own
it. Building it lazily on first use, rather than at import time, is the simplest way to guarantee
that.

### Celery / worker processes

Same fork rule. Build lazily per worker process, not at module import in the parent.

## Supplying your own transport

The transport is a `Protocol` — a small structural interface, not a base class to inherit. The sync
one requires `send(request) -> HttpResponse` and `close()`; the async one `send` and `aclose()`.
Anything satisfying that shape is accepted:

```python
client = {Api}Client(custom_http_client=MyTransport(), oauth2=...)
```

This is the seam for **logging, tracing, metrics and tests** — the Python equivalent of a
`DelegatingHandler`. Wrap the SDK's own httpx transport rather than reimplementing HTTP:

```python
class LoggingTransport:
    def __init__(self, inner): self._inner = inner
    def send(self, request):
        response = self._inner.send(request)
        log.info("%s %s -> %s", request.method, request.url, response.status_code)
        return response
    def close(self): self._inner.close()
```

**If you pass `custom_http_client`, the `timeout=` you passed to the client no longer reaches the
wire.** The client's `timeout` is used to construct *its own default* transport; supply your own and
its timeout is whatever you configured on it. Set the timeout on the transport you pass, or you have
silently reverted to that library's default. See `python-configuration-resilience`.

The **per-call** timeout is the exception: `request_options={"timeout": …}` travels on the request
object itself (`HttpRequest.timeout`), so it reaches whatever transport you supplied — and a
transport of your own is obliged to honour it, falling back to its own timeout when it is `None`.
A wrapper that forwards the request unchanged satisfies that for free; one that rebuilds the request
must carry `timeout` across.

You also own a transport you supply: the client's `close()` calls it, but only if it is still the
client's to call — do not close it yourself while the client is alive.

## Next

- Credentials and the token lifecycle → **python-authentication**
- Making calls, sync and async → **python-calling-endpoints**
- Timeouts, retries (there are none), proxies, logging → **python-configuration-resilience**
