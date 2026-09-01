<!-- Generated file — do not edit; regenerated with the SDK. -->

# SalesCommissions — operations

Accessor: `client.sales_commissions` · Source: `maxio_advanced_billing/apis/sales_commissions.py` · 3 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.sales_commissions.list_sales_commission_settings

- **Route**: `GET /sellers/{seller_id}/sales_commission_settings.json`
- **Server**: `production`
- **Signature**: `def list_sales_commission_settings(seller_id: str, *, live_mode: bool | None = None, page: int | None = 1, per_page: int | None = 100, authorization: str | None = "Bearer <<apiKey>>", request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `seller_id`
- **Params**: `seller_id` — path · `live_mode` — query · `page` — query · `per_page` — query · `authorization` — header `Authorization`
- **Returns (parsed)**: `list[SaleRepSettings]`
- **Returns (raw)**: `ApiResult[list[SaleRepSettings], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SaleRepSettings` | `maxio_advanced_billing/models/sale_rep_settings.py` |

### client.sales_commissions.list_sales_reps

- **Route**: `GET /sellers/{seller_id}/sales_reps.json`
- **Server**: `production`
- **Signature**: `def list_sales_reps(seller_id: str, *, live_mode: bool | None = None, page: int | None = 1, per_page: int | None = 100, authorization: str | None = "Bearer <<apiKey>>", request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `seller_id`
- **Params**: `seller_id` — path · `live_mode` — query · `page` — query · `per_page` — query · `authorization` — header `Authorization`
- **Returns (parsed)**: `list[ListSaleRepItem]`
- **Returns (raw)**: `ApiResult[list[ListSaleRepItem], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ListSaleRepItem` | `maxio_advanced_billing/models/list_sale_rep_item.py` |

### client.sales_commissions.read_sales_rep

- **Route**: `GET /sellers/{seller_id}/sales_reps/{sales_rep_id}.json`
- **Server**: `production`
- **Signature**: `def read_sales_rep(seller_id: str, sales_rep_id: str, *, live_mode: bool | None = None, page: int | None = 1, per_page: int | None = 100, authorization: str | None = "Bearer <<apiKey>>", request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `seller_id`, `sales_rep_id`
- **Params**: `seller_id` — path · `sales_rep_id` — path · `live_mode` — query · `page` — query · `per_page` — query · `authorization` — header `Authorization`
- **Returns (parsed)**: `SaleRep`
- **Returns (raw)**: `ApiResult[SaleRep, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SaleRep` | `maxio_advanced_billing/models/sale_rep.py` |

