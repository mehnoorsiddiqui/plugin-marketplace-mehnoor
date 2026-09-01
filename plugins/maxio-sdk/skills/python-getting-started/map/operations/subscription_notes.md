<!-- Generated file — do not edit; regenerated with the SDK. -->

# SubscriptionNotes — operations

Accessor: `client.subscription_notes` · Source: `maxio_advanced_billing/apis/subscription_notes.py` · 5 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded, and an operation with no table mentions nothing but builtins and those.

### client.subscription_notes.create_subscription_note

- **Route**: `POST /subscriptions/{subscription_id}/notes.json`
- **Server**: `production`
- **Signature**: `def create_subscription_note(subscription_id: int, *, body: UpdateSubscriptionNoteRequest | UpdateSubscriptionNoteRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionNoteResponse`
- **Returns (raw)**: `ApiResult[SubscriptionNoteResponse, CreateSubscriptionNoteErrorBody]`
- **Error**: `CreateSubscriptionNoteErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `UpdateSubscriptionNoteRequest` | `maxio_advanced_billing/models/update_subscription_note_request.py` |
| `UpdateSubscriptionNoteRequestDict` | `maxio_advanced_billing/models/update_subscription_note_request.py` |
| `SubscriptionNoteResponse` | `maxio_advanced_billing/models/subscription_note_response.py` |
| `CreateSubscriptionNoteErrorBody` | `maxio_advanced_billing/errors/create_subscription_note_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_notes.delete_subscription_note

- **Route**: `DELETE /subscriptions/{subscription_id}/notes/{note_id}.json`
- **Server**: `production`
- **Signature**: `def delete_subscription_note(subscription_id: int, note_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `note_id`
- **Params**: `subscription_id` — path · `note_id` — path
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, RawError]`
- **Error**: `RawError` — **Case B**

### client.subscription_notes.list_subscription_notes

- **Route**: `GET /subscriptions/{subscription_id}/notes.json`
- **Server**: `production`
- **Signature**: `def list_subscription_notes(subscription_id: int, *, page: int | None = 1, per_page: int | None = 20, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `page` — query · `per_page` — query
- **Returns (parsed)**: `list[SubscriptionNoteResponse]`
- **Returns (raw)**: `ApiResult[list[SubscriptionNoteResponse], ListSubscriptionNotesErrorBody]`
- **Error**: `ListSubscriptionNotesErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `SubscriptionNoteResponse` | `maxio_advanced_billing/models/subscription_note_response.py` |
| `ListSubscriptionNotesErrorBody` | `maxio_advanced_billing/errors/list_subscription_notes_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_notes.read_subscription_note

- **Route**: `GET /subscriptions/{subscription_id}/notes/{note_id}.json`
- **Server**: `production`
- **Signature**: `def read_subscription_note(subscription_id: int, note_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `note_id`
- **Params**: `subscription_id` — path · `note_id` — path
- **Returns (parsed)**: `SubscriptionNoteResponse`
- **Returns (raw)**: `ApiResult[SubscriptionNoteResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SubscriptionNoteResponse` | `maxio_advanced_billing/models/subscription_note_response.py` |

### client.subscription_notes.update_subscription_note

- **Route**: `PUT /subscriptions/{subscription_id}/notes/{note_id}.json`
- **Server**: `production`
- **Signature**: `def update_subscription_note(subscription_id: int, note_id: int, *, body: UpdateSubscriptionNoteRequest | UpdateSubscriptionNoteRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `note_id`
- **Params**: `subscription_id` — path · `note_id` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionNoteResponse`
- **Returns (raw)**: `ApiResult[SubscriptionNoteResponse, UpdateSubscriptionNoteErrorBody]`
- **Error**: `UpdateSubscriptionNoteErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `UpdateSubscriptionNoteRequest` | `maxio_advanced_billing/models/update_subscription_note_request.py` |
| `UpdateSubscriptionNoteRequestDict` | `maxio_advanced_billing/models/update_subscription_note_request.py` |
| `SubscriptionNoteResponse` | `maxio_advanced_billing/models/subscription_note_response.py` |
| `UpdateSubscriptionNoteErrorBody` | `maxio_advanced_billing/errors/update_subscription_note_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

