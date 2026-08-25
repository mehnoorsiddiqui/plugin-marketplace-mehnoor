---
name: python-testing
description: Testing code that calls an APIMatic-generated Python SDK — which seam to fake (the transport protocol, or respx at the httpx layer), asserting on the request the SDK actually built, covering the error and decode-failure paths, and keeping tests independent of SDK internals. Load before writing tests for the integration layer.
---

# Testing code that calls an APIMatic Python SDK

> `{...}` is a placeholder for a name from your SDK.

## Fake the transport, not the client

The SDK gives you one designed seam: the **transport protocol**. It is a `Protocol`, so a fake needs no
base class and no `Mock` — just the right methods:

```python
from {root_package}.core import HttpRequest, HttpResponse

class FakeTransport:
    def __init__(self, *responses: HttpResponse) -> None:
        self._responses = list(responses)
        self.requests: list[HttpRequest] = []

    def send(self, request: HttpRequest) -> HttpResponse:
        self.requests.append(request)
        return self._responses.pop(0)

    def close(self) -> None: ...

def json_response(status: int, body: dict) -> HttpResponse:
    return HttpResponse(
        status_code=status,
        headers={"content-type": "application/json"},   # lowercase keys, per the contract
        content=json.dumps(body).encode(),
    )
```

`HttpResponse` defaults `content` to `b""` and `request` to `None`, so an empty `204` is just
`HttpResponse(status_code=204, headers={})` — which is what you want for the 11 operations that
return `None` (`python-calling-endpoints`).

Inject it and you have a real client with no network:

```python
transport = FakeTransport(json_response(201, {"id": "5O1", "status": "CREATED"}))
client = {Api}Client(custom_http_client=transport, oauth2={"client_id": "x", "client_secret": "y"})
```

**Do not patch the client's private attributes** (`client._raw_client`, `_auth`, …) and do not
`Mock(spec={Api}Client)`. Both couple your tests to internals the generator is free to change, and a
mocked client cannot catch the mistakes that actually happen — a wrong path parameter, a body that
does not serialize, a header you forgot. Faking the transport exercises the real request-building
pipeline; that is the whole point.

**Mind the token request.** With `oauth2=` set, the first call fetches a token, so the *first* request
your fake sees is the `POST` to the token endpoint — not your operation. Two options: queue a token
response first, or bypass auth in tests with a stub token source.

```python
transport = FakeTransport(
    json_response(200, {"access_token": "t", "token_type": "Bearer", "expires_in": 3600}),
    json_response(201, {"id": "5O1", "status": "CREATED"}),
)
...
assert len(transport.requests) == 2
order_request = transport.requests[-1]        # index from the end, not [0]
```

Forgetting this is the most common way a first test fails with a confusing error: the operation's
decoder tries to read a token body, and you get a `ValidationError` about missing `id`.

## The alternative: `respx`

Because the default transport is httpx, `respx` mocks at the HTTP layer and needs no injection:

```python
import respx, httpx

@respx.mock
def test_create_order():
    respx.post("https://api-m.sandbox.paypal.com/v1/oauth2/token").mock(
        return_value=httpx.Response(200, json={"access_token": "t", "token_type": "Bearer"})
    )
    route = respx.post("https://api-m.sandbox.paypal.com/v2/checkout/orders").mock(
        return_value=httpx.Response(201, json={"id": "5O1", "status": "CREATED"})
    )
    order = service.create_order()          # production code, unmodified
    assert route.called
    assert json.loads(route.calls.last.request.content)["intent"] == "CAPTURE"
```

Pick one and be consistent. `respx` asserts on real URLs and is closer to the wire; the transport fake
is dependency-free, faster, and keeps working if the SDK ever changes HTTP library. Use `respx` when
you want URL-level matching, the fake when you are unit-testing your own logic.

## Assert on behaviour, not on execution

`assert transport.requests` proves only that something was sent. Assert the things a regression would
actually change:

```python
req = transport.requests[-1]
assert req.method == "POST"
assert req.url.endswith("/v2/checkout/orders")
assert req.body.value["intent"] == "CAPTURE"                 # JsonBody carries the dumped payload
assert req.body.value["purchase_units"][0]["amount"]["currency_code"] == "USD"
assert req.headers["authorization"] == "Bearer <token>"      # lowercase key — see below
assert req.headers["prefer"] == "return=minimal"             # a real default reached the wire
```

**`HttpRequest.headers` keys are lowercased, not just the response's.** Every layer — the API's, the
endpoint's, the auth scheme's and your own `extra_headers` — is normalized before the merge, so
`req.headers["Authorization"]` is a `KeyError` and `req.headers["authorization"]` is the assertion
you want. This is the single most common way a first request assertion fails confusingly. (The
defensive `"authorization" in {k.lower() for k in req.headers}` also works, but there is no case to
defend against — assert the lowercase key directly.)

Note the body assertion uses **wire aliases**, because that is what serialization produces. If a
model's Python name differs from its JSON name, the request body has the JSON name — do not "fix" that
in the test.

Worth asserting once, somewhere, because they are silent when wrong:

- **Idempotency keys**: the same logical operation retried sends the *same* key, and two different
  operations send different ones.
- **The value actually reached the wire.** A misspelled optional model member becomes a preserved extra
  field rather than an error (`python-models`), so the only thing that catches it is an assertion on
  the serialized body.
- **Timeout plumbing**: `req.timeout` reflects a per-call `request_options={"timeout": ...}` — and
  **only** that. The client's own `timeout=` never rides the request (it configures the SDK's default
  transport), so `req.timeout is None` on an ordinary call is correct, not a bug. A fake transport
  that asserts on the client-level timeout is asserting something that was never there.
- **Bodies that cannot serialize.** A model the SDK cannot dump raises before anything is sent, so
  the failure never reaches your fake. Worth one test where it applies: an `Optional[Any]` field left
  unset (`Patch(op="remove", path=…)`) raises `PydanticSerializationError` and `transport.requests`
  stays empty. See `python-models`.

## Cover the error paths — all four kinds

The error surface has four distinct shapes (`python-error-handling`), and code that handles only the
first is the norm. Each is cheap to simulate:

```python
def test_rejected_order_maps_to_client_error():
    transport = FakeTransport(
        token_response(),
        json_response(422, {"name": "UNPROCESSABLE_ENTITY", "message": "…", "debug_id": "d1"}),
    )
    with pytest.raises(MyProviderRejected) as exc:
        service.create_order()
    assert exc.value.status_code == 422

def test_unmapped_status_is_raw():
    # A status the operation does not document -> the RawError arm of the union.
    transport = FakeTransport(token_response(), json_response(418, {"whatever": 1}))
    ...

def test_truncated_success_body_is_not_reported_as_success():
    # A 2xx missing `id` does NOT raise: 16 of the 17 response models declare no required
    # member, so this decodes fine and `order.id` reads back UNSET. Your own guard is the
    # only thing that catches it (python-error-handling).
    transport = FakeTransport(token_response(), json_response(201, {"status": "CREATED"}))
    with pytest.raises(MyProviderUnreadable):
        service.create_order()

def test_unreadable_success_body_is_not_reported_as_failure():
    # A real decode failure needs a TYPE mismatch, not an absent field -> ValidationError,
    # NOT ApiError, in BOTH response modes.
    transport = FakeTransport(token_response(), json_response(201, {"purchase_units": "not-a-list"}))
    with pytest.raises(MyProviderUnreadable):
        service.create_order()

def test_transport_failure_is_unknown_outcome():
    class Boom:
        def send(self, request): raise httpx.ConnectError("refused")
        def close(self): ...
    ...
```

And the one people forget: **bad credentials**. Return a `401` with an RFC 6749 body from the *token*
request and assert your configuration error, not an order-rejection error:

```python
def test_bad_credentials_is_a_config_error():
    transport = FakeTransport(json_response(401, {"error": "invalid_client"}))
    with pytest.raises(MyProviderConfigError):
        service.create_order()
```

## Async tests

Same seam, async shape — `async def send`, and `aclose` rather than `close`:

```python
class FakeAsyncTransport:
    def __init__(self, *responses): self._responses = list(responses); self.requests = []
    async def send(self, request):
        self.requests.append(request)
        return self._responses.pop(0)
    async def aclose(self) -> None: ...

@pytest.mark.asyncio
async def test_create_order():
    client = Async{Api}Client(custom_async_http_client=FakeAsyncTransport(...), oauth2=...)
```

Use `pytest-asyncio` (or `anyio`). The type checker will reject a sync fake passed to the async client
and vice versa, which is a genuine safety net — do not silence it with a `type: ignore`.

## Keeping tests independent of SDK internals

- **Never import from a private module.** Everything you need is re-exported from `{root_package}.core`,
  `.models`, `.models.enums` and `.errors`. An import from `…core.results` will break.
- **Never assert on `str(e)` or `repr(e)`** of an SDK exception. They are deliberately terse (status and
  payload type name only) and are not a stable contract. Assert on `e.status_code` and the narrowed
  `e.error` fields.
- **Never introspect `model_fields`** to build test data. Construct models explicitly; a test that
  derives its payload from the model's own definition cannot detect a wrong payload.
- **Build fixtures with models, then serialize** — `order_request_fixture().to_dict()` — rather than
  hand-writing wire JSON, so a member rename fails the fixture rather than passing a stale test.
- **Test your own boundary's output, not the SDK's.** The valuable assertions are that a provider 4xx
  becomes your 4xx and a transport failure becomes your 5xx. That the SDK raises `ApiError` on a 422 is
  the SDK's own tested behaviour, not yours.

## Integration tests against sandbox

Keep them separate from unit tests (`-m integration`), skip when credentials are absent, and never
assert on ids or timestamps the provider generates:

```python
@pytest.mark.integration
@pytest.mark.skipif(not os.getenv("PAYPAL_CLIENT_ID"), reason="sandbox credentials not configured")
def test_real_create_order():
    with {Api}Client(oauth2=..., timeout=15.0) as client:
        order = client.orders.create_order(...)
        assert order.id
```

Point them at sandbox explicitly rather than relying on the default, so the test states which
environment it needs. Expect flakiness from the provider, not from your code, and never gate CI on a
third party's uptime unless you mean to.
