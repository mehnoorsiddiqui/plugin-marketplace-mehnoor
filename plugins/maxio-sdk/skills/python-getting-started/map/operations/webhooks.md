<!-- Generated file — do not edit; regenerated with the SDK. -->

# Webhooks — operations

Accessor: `client.webhooks` · Source: `maxio_advanced_billing/apis/webhooks.py` · 6 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.webhooks.create_endpoint

- **Route**: `POST /endpoints.json`
- **Server**: `production`
- **Signature**: `def create_endpoint(*, body: CreateOrUpdateEndpointRequest | CreateOrUpdateEndpointRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `body` — JSON body
- **Returns (parsed)**: `EndpointResponse`
- **Returns (raw)**: `ApiResult[EndpointResponse, CreateEndpointErrorBody]`
- **Error**: `CreateEndpointErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateOrUpdateEndpointRequest` | `maxio_advanced_billing/models/create_or_update_endpoint_request.py` |
| `CreateOrUpdateEndpointRequestDict` | `maxio_advanced_billing/models/create_or_update_endpoint_request.py` |
| `EndpointResponse` | `maxio_advanced_billing/models/endpoint_response.py` |
| `CreateEndpointErrorBody` | `maxio_advanced_billing/errors/create_endpoint_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.webhooks.enable_webhooks

- **Route**: `PUT /webhooks/settings.json`
- **Server**: `production`
- **Signature**: `def enable_webhooks(*, body: EnableWebhooksRequest | EnableWebhooksRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `body` — JSON body
- **Returns (parsed)**: `EnableWebhooksResponse`
- **Returns (raw)**: `ApiResult[EnableWebhooksResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `EnableWebhooksRequest` | `maxio_advanced_billing/models/enable_webhooks_request.py` |
| `EnableWebhooksRequestDict` | `maxio_advanced_billing/models/enable_webhooks_request.py` |
| `EnableWebhooksResponse` | `maxio_advanced_billing/models/enable_webhooks_response.py` |

### client.webhooks.list_endpoints

- **Route**: `GET /endpoints.json`
- **Server**: `production`
- **Signature**: `def list_endpoints(*, request_options: RequestOptionsOrDict | None = None)`
- **Returns (parsed)**: `list[Endpoint]`
- **Returns (raw)**: `ApiResult[list[Endpoint], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `Endpoint` | `maxio_advanced_billing/models/endpoint.py` |

### client.webhooks.list_webhooks

- **Route**: `GET /webhooks.json`
- **Server**: `production`
- **Signature**: `def list_webhooks(*, status: WebhookStatusOrStr | None = None, since_date: str | None = None, until_date: str | None = None, page: int | None = 1, per_page: int | None = 20, order: WebhookOrderOrStr | None = None, subscription: int | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `status` — query · `since_date` — query · `until_date` — query · `page` — query · `per_page` — query · `order` — query · `subscription` — query
- **Returns (parsed)**: `list[WebhookResponse]`
- **Returns (raw)**: `ApiResult[list[WebhookResponse], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `WebhookStatusOrStr` | `maxio_advanced_billing/models/enums/webhook_status.py` |
| `WebhookOrderOrStr` | `maxio_advanced_billing/models/enums/webhook_order.py` |
| `WebhookResponse` | `maxio_advanced_billing/models/webhook_response.py` |

### client.webhooks.replay_webhooks

- **Route**: `POST /webhooks/replay.json`
- **Server**: `production`
- **Signature**: `def replay_webhooks(*, body: ReplayWebhooksRequest | ReplayWebhooksRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `body` — JSON body
- **Returns (parsed)**: `ReplayWebhooksResponse`
- **Returns (raw)**: `ApiResult[ReplayWebhooksResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ReplayWebhooksRequest` | `maxio_advanced_billing/models/replay_webhooks_request.py` |
| `ReplayWebhooksRequestDict` | `maxio_advanced_billing/models/replay_webhooks_request.py` |
| `ReplayWebhooksResponse` | `maxio_advanced_billing/models/replay_webhooks_response.py` |

### client.webhooks.update_endpoint

- **Route**: `PUT /endpoints/{endpoint_id}.json`
- **Server**: `production`
- **Signature**: `def update_endpoint(endpoint_id: int, *, body: CreateOrUpdateEndpointRequest | CreateOrUpdateEndpointRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `endpoint_id`
- **Params**: `endpoint_id` — path · `body` — JSON body
- **Returns (parsed)**: `EndpointResponse`
- **Returns (raw)**: `ApiResult[EndpointResponse, UpdateEndpointErrorBody]`
- **Error**: `UpdateEndpointErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `CreateOrUpdateEndpointRequest` | `maxio_advanced_billing/models/create_or_update_endpoint_request.py` |
| `CreateOrUpdateEndpointRequestDict` | `maxio_advanced_billing/models/create_or_update_endpoint_request.py` |
| `EndpointResponse` | `maxio_advanced_billing/models/endpoint_response.py` |
| `UpdateEndpointErrorBody` | `maxio_advanced_billing/errors/update_endpoint_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

