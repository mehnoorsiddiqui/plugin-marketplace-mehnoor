<!-- Generated file — do not edit; regenerated with the SDK. -->

# Events — operations

Accessor: `client.events` · Source: `maxio_advanced_billing/apis/events.py` · 3 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.events.list_events

- **Route**: `GET /events.json`
- **Server**: `production`
- **Signature**: `def list_events(*, page: int | None = 1, per_page: int | None = 20, since_id: int | None = None, max_id: int | None = None, direction: DirectionOrStr | None = None, filter: list[EventKeyOrStr] | None = None, date_field: ListEventsDateFieldOrStr | None = None, start_date: str | None = None, end_date: str | None = None, start_datetime: str | None = None, end_datetime: str | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `page` — query · `per_page` — query · `since_id` — query · `max_id` — query · `direction` — query · `filter` — query · `date_field` — query · `start_date` — query · `end_date` — query · `start_datetime` — query · `end_datetime` — query
- **Returns (parsed)**: `list[EventResponse]`
- **Returns (raw)**: `ApiResult[list[EventResponse], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `DirectionOrStr` | `maxio_advanced_billing/models/enums/direction.py` |
| `EventKeyOrStr` | `maxio_advanced_billing/models/enums/event_key.py` |
| `ListEventsDateFieldOrStr` | `maxio_advanced_billing/models/enums/list_events_date_field.py` |
| `EventResponse` | `maxio_advanced_billing/models/event_response.py` |

### client.events.list_subscription_events

- **Route**: `GET /subscriptions/{subscription_id}/events.json`
- **Server**: `production`
- **Signature**: `def list_subscription_events(subscription_id: int, *, page: int | None = 1, per_page: int | None = 20, since_id: int | None = None, max_id: int | None = None, direction: DirectionOrStr | None = None, filter: list[EventKeyOrStr] | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `page` — query · `per_page` — query · `since_id` — query · `max_id` — query · `direction` — query · `filter` — query
- **Returns (parsed)**: `list[EventResponse]`
- **Returns (raw)**: `ApiResult[list[EventResponse], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `DirectionOrStr` | `maxio_advanced_billing/models/enums/direction.py` |
| `EventKeyOrStr` | `maxio_advanced_billing/models/enums/event_key.py` |
| `EventResponse` | `maxio_advanced_billing/models/event_response.py` |

### client.events.read_events_count

- **Route**: `GET /events/count.json`
- **Server**: `production`
- **Signature**: `def read_events_count(*, page: int | None = 1, per_page: int | None = 20, since_id: int | None = None, max_id: int | None = None, direction: DirectionOrStr | None = None, filter: list[EventKeyOrStr] | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `page` — query · `per_page` — query · `since_id` — query · `max_id` — query · `direction` — query · `filter` — query
- **Returns (parsed)**: `CountResponse`
- **Returns (raw)**: `ApiResult[CountResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `DirectionOrStr` | `maxio_advanced_billing/models/enums/direction.py` |
| `EventKeyOrStr` | `maxio_advanced_billing/models/enums/event_key.py` |
| `CountResponse` | `maxio_advanced_billing/models/count_response.py` |

