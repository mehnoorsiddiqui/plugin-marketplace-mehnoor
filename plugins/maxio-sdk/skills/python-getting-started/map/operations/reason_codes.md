<!-- Generated file — do not edit; regenerated with the SDK. -->

# ReasonCodes — operations

Accessor: `client.reason_codes` · Source: `maxio_advanced_billing/apis/reason_codes.py` · 5 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.reason_codes.create_reason_code

- **Route**: `POST /reason_codes.json`
- **Server**: `production`
- **Signature**: `def create_reason_code(*, body: CreateReasonCodeRequest | CreateReasonCodeRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `body` — JSON body
- **Returns (parsed)**: `ReasonCodeResponse`
- **Returns (raw)**: `ApiResult[ReasonCodeResponse, CreateReasonCodeErrorBody]`
- **Error**: `CreateReasonCodeErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateReasonCodeRequest` | `maxio_advanced_billing/models/create_reason_code_request.py` |
| `CreateReasonCodeRequestDict` | `maxio_advanced_billing/models/create_reason_code_request.py` |
| `ReasonCodeResponse` | `maxio_advanced_billing/models/reason_code_response.py` |
| `CreateReasonCodeErrorBody` | `maxio_advanced_billing/errors/create_reason_code_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.reason_codes.delete_reason_code

- **Route**: `DELETE /reason_codes/{reason_code_id}.json`
- **Server**: `production`
- **Signature**: `def delete_reason_code(reason_code_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `reason_code_id`
- **Params**: `reason_code_id` — path
- **Returns (parsed)**: `OkResponse`
- **Returns (raw)**: `ApiResult[OkResponse, DeleteReasonCodeErrorBody]`
- **Error**: `DeleteReasonCodeErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `OkResponse` | `maxio_advanced_billing/models/ok_response.py` |
| `DeleteReasonCodeErrorBody` | `maxio_advanced_billing/errors/delete_reason_code_error.py` |

### client.reason_codes.list_reason_codes

- **Route**: `GET /reason_codes.json`
- **Server**: `production`
- **Signature**: `def list_reason_codes(*, page: int | None = 1, per_page: int | None = 20, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `page` — query · `per_page` — query
- **Returns (parsed)**: `list[ReasonCodeResponse]`
- **Returns (raw)**: `ApiResult[list[ReasonCodeResponse], ListReasonCodesErrorBody]`
- **Error**: `ListReasonCodesErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ReasonCodeResponse` | `maxio_advanced_billing/models/reason_code_response.py` |
| `ListReasonCodesErrorBody` | `maxio_advanced_billing/errors/list_reason_codes_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.reason_codes.read_reason_code

- **Route**: `GET /reason_codes/{reason_code_id}.json`
- **Server**: `production`
- **Signature**: `def read_reason_code(reason_code_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `reason_code_id`
- **Params**: `reason_code_id` — path
- **Returns (parsed)**: `ReasonCodeResponse`
- **Returns (raw)**: `ApiResult[ReasonCodeResponse, ReadReasonCodeErrorBody]`
- **Error**: `ReadReasonCodeErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `ReasonCodeResponse` | `maxio_advanced_billing/models/reason_code_response.py` |
| `ReadReasonCodeErrorBody` | `maxio_advanced_billing/errors/read_reason_code_error.py` |

### client.reason_codes.update_reason_code

- **Route**: `PUT /reason_codes/{reason_code_id}.json`
- **Server**: `production`
- **Signature**: `def update_reason_code(reason_code_id: int, *, body: UpdateReasonCodeRequest | UpdateReasonCodeRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `reason_code_id`
- **Params**: `reason_code_id` — path · `body` — JSON body
- **Returns (parsed)**: `ReasonCodeResponse`
- **Returns (raw)**: `ApiResult[ReasonCodeResponse, UpdateReasonCodeErrorBody]`
- **Error**: `UpdateReasonCodeErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `UpdateReasonCodeRequest` | `maxio_advanced_billing/models/update_reason_code_request.py` |
| `UpdateReasonCodeRequestDict` | `maxio_advanced_billing/models/update_reason_code_request.py` |
| `ReasonCodeResponse` | `maxio_advanced_billing/models/reason_code_response.py` |
| `UpdateReasonCodeErrorBody` | `maxio_advanced_billing/errors/update_reason_code_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

