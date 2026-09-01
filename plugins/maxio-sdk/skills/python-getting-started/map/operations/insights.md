<!-- Generated file — do not edit; regenerated with the SDK. -->

# Insights — operations

Accessor: `client.insights` · Source: `maxio_advanced_billing/apis/insights.py` · 4 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.insights.list_mrr_movements

- **Route**: `GET /mrr_movements.json`
- **Server**: `production`
- **Signature**: `def list_mrr_movements(*, subscription_id: int | None = None, page: int | None = 1, per_page: int | None = 10, direction: SortingDirectionOrStr | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `subscription_id` — query · `page` — query · `per_page` — query · `direction` — query
- **Returns (parsed)**: `ListMrrResponse`
- **Returns (raw)**: `ApiResult[ListMrrResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SortingDirectionOrStr` | `maxio_advanced_billing/models/enums/sorting_direction.py` |
| `ListMrrResponse` | `maxio_advanced_billing/models/list_mrr_response.py` |

### client.insights.list_mrr_per_subscription

- **Route**: `GET /subscriptions_mrr.json`
- **Server**: `production`
- **Signature**: `def list_mrr_per_subscription(*, filter: ListMrrFilter | ListMrrFilterDict | None = None, at_time: str | None = None, page: int | None = 1, per_page: int | None = 20, direction: DirectionOrStr | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `filter` — query · `at_time` — query · `page` — query · `per_page` — query · `direction` — query
- **Returns (parsed)**: `SubscriptionMrrResponse`
- **Returns (raw)**: `ApiResult[SubscriptionMrrResponse, ListMrrPerSubscriptionErrorBody]`
- **Error**: `ListMrrPerSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `SubscriptionsMrrErrorResponse1` [400] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ListMrrFilter` | `maxio_advanced_billing/models/list_mrr_filter.py` |
| `ListMrrFilterDict` | `maxio_advanced_billing/models/list_mrr_filter.py` |
| `DirectionOrStr` | `maxio_advanced_billing/models/enums/direction.py` |
| `SubscriptionMrrResponse` | `maxio_advanced_billing/models/subscription_mrr_response.py` |
| `ListMrrPerSubscriptionErrorBody` | `maxio_advanced_billing/errors/list_mrr_per_subscription_error.py` |
| `SubscriptionsMrrErrorResponse1` | `maxio_advanced_billing/models/subscriptions_mrr_error_response1.py` |

### client.insights.read_mrr

- **Route**: `GET /mrr.json`
- **Server**: `production`
- **Signature**: `def read_mrr(*, at_time: RFC3339DateTime | None = None, subscription_id: int | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `at_time` — query · `subscription_id` — query
- **Returns (parsed)**: `MrrResponse`
- **Returns (raw)**: `ApiResult[MrrResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `MrrResponse` | `maxio_advanced_billing/models/mrr_response.py` |

### client.insights.read_site_stats

- **Route**: `GET /stats.json`
- **Server**: `production`
- **Signature**: `def read_site_stats(*, request_options: RequestOptionsOrDict | None = None)`
- **Returns (parsed)**: `SiteSummary`
- **Returns (raw)**: `ApiResult[SiteSummary, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SiteSummary` | `maxio_advanced_billing/models/site_summary.py` |

