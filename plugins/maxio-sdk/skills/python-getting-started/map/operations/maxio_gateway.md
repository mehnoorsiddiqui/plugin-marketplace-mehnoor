<!-- Generated file — do not edit; regenerated with the SDK. -->

# MaxioGateway — operations

Accessor: `client.maxio_gateway` · Source: `maxio_advanced_billing/apis/maxio_gateway.py` · 1 operation

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.maxio_gateway.request_access_token

- **Route**: `POST /oauth/token`
- **Server**: `oauth`
- **Signature**: `def request_access_token(body: MaxioGatewayOauthTokenRequest | MaxioGatewayOauthTokenRequestDict, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `body`
- **Params**: `body` — JSON body
- **Returns (parsed)**: `MaxioGatewayOauthAccessToken`
- **Returns (raw)**: `ApiResult[MaxioGatewayOauthAccessToken, RequestAccessTokenErrorBody]`
- **Error**: `RequestAccessTokenErrorBody` — **Case A (typed)**
- **Error arms**: `MaxioGatewayOauthError` [400, 401] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `MaxioGatewayOauthTokenRequest` | `maxio_advanced_billing/models/maxio_gateway_oauth_token_request.py` |
| `MaxioGatewayOauthTokenRequestDict` | `maxio_advanced_billing/models/maxio_gateway_oauth_token_request.py` |
| `MaxioGatewayOauthAccessToken` | `maxio_advanced_billing/models/maxio_gateway_oauth_access_token.py` |
| `RequestAccessTokenErrorBody` | `maxio_advanced_billing/errors/request_access_token_error.py` |
| `MaxioGatewayOauthError` | `maxio_advanced_billing/models/maxio_gateway_oauth_error.py` |

