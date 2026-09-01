<!-- Generated file — do not edit; regenerated with the SDK. -->

# ComponentPricePoints — operations

Accessor: `client.component_price_points` · Source: `maxio_advanced_billing/apis/component_price_points.py` · 12 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.component_price_points.archive_component_price_point

- **Route**: `DELETE /components/{component_id}/price_points/{price_point_id}.json`
- **Server**: `production`
- **Signature**: `def archive_component_price_point(component_id: ComponentIdModel | ComponentIdModelDict, price_point_id: PricePointIdModel | PricePointIdModelDict, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `component_id`, `price_point_id`
- **Params**: `component_id` — path · `price_point_id` — path
- **Returns (parsed)**: `ComponentPricePointResponse`
- **Returns (raw)**: `ApiResult[ComponentPricePointResponse, ArchiveComponentPricePointErrorBody]`
- **Error**: `ArchiveComponentPricePointErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ComponentIdModel` | `maxio_advanced_billing/models/unions/component_id_model.py` |
| `ComponentIdModelDict` | `maxio_advanced_billing/models/unions/component_id_model.py` |
| `PricePointIdModel` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `PricePointIdModelDict` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `ComponentPricePointResponse` | `maxio_advanced_billing/models/component_price_point_response.py` |
| `ArchiveComponentPricePointErrorBody` | `maxio_advanced_billing/errors/archive_component_price_point_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.component_price_points.bulk_create_component_price_points

- **Route**: `POST /components/{component_id}/price_points/bulk.json`
- **Server**: `production`
- **Signature**: `def bulk_create_component_price_points(component_id: str, *, body: CreateComponentPricePointsRequest | CreateComponentPricePointsRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `component_id`
- **Params**: `component_id` — path · `body` — JSON body
- **Returns (parsed)**: `ComponentPricePointsResponse`
- **Returns (raw)**: `ApiResult[ComponentPricePointsResponse, BulkCreateComponentPricePointsErrorBody]`
- **Error**: `BulkCreateComponentPricePointsErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateComponentPricePointsRequest` | `maxio_advanced_billing/models/create_component_price_points_request.py` |
| `CreateComponentPricePointsRequestDict` | `maxio_advanced_billing/models/create_component_price_points_request.py` |
| `ComponentPricePointsResponse` | `maxio_advanced_billing/models/component_price_points_response.py` |
| `BulkCreateComponentPricePointsErrorBody` | `maxio_advanced_billing/errors/bulk_create_component_price_points_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.component_price_points.clone_component_price_point

- **Route**: `POST /components/{component_id}/price_points/{price_point_id}/clone.json`
- **Server**: `production`
- **Signature**: `def clone_component_price_point(component_id: ComponentIdModel | ComponentIdModelDict, price_point_id: PricePointIdModel | PricePointIdModelDict, *, body: CloneComponentPricePointRequest | CloneComponentPricePointRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `component_id`, `price_point_id`
- **Params**: `component_id` — path · `price_point_id` — path · `body` — JSON body
- **Returns (parsed)**: `ComponentPricePointCurrencyOverageResponse`
- **Returns (raw)**: `ApiResult[ComponentPricePointCurrencyOverageResponse, CloneComponentPricePointErrorBody]`
- **Error**: `CloneComponentPricePointErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `ComponentIdModel` | `maxio_advanced_billing/models/unions/component_id_model.py` |
| `ComponentIdModelDict` | `maxio_advanced_billing/models/unions/component_id_model.py` |
| `PricePointIdModel` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `PricePointIdModelDict` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `CloneComponentPricePointRequest` | `maxio_advanced_billing/models/clone_component_price_point_request.py` |
| `CloneComponentPricePointRequestDict` | `maxio_advanced_billing/models/clone_component_price_point_request.py` |
| `ComponentPricePointCurrencyOverageResponse` | `maxio_advanced_billing/models/component_price_point_currency_overage_response.py` |
| `CloneComponentPricePointErrorBody` | `maxio_advanced_billing/errors/clone_component_price_point_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.component_price_points.create_component_price_point

- **Route**: `POST /components/{component_id}/price_points.json`
- **Server**: `production`
- **Signature**: `def create_component_price_point(component_id: int, *, body: CreateComponentPricePointRequest | CreateComponentPricePointRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `component_id`
- **Params**: `component_id` — path · `body` — JSON body
- **Returns (parsed)**: `ComponentPricePointResponse`
- **Returns (raw)**: `ApiResult[ComponentPricePointResponse, CreateComponentPricePointErrorBody]`
- **Error**: `CreateComponentPricePointErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorArrayMapResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateComponentPricePointRequest` | `maxio_advanced_billing/models/create_component_price_point_request.py` |
| `CreateComponentPricePointRequestDict` | `maxio_advanced_billing/models/create_component_price_point_request.py` |
| `ComponentPricePointResponse` | `maxio_advanced_billing/models/component_price_point_response.py` |
| `CreateComponentPricePointErrorBody` | `maxio_advanced_billing/errors/create_component_price_point_error.py` |
| `ErrorArrayMapResponse1` | `maxio_advanced_billing/models/error_array_map_response1.py` |

### client.component_price_points.create_currency_prices

- **Route**: `POST /price_points/{price_point_id}/currency_prices.json`
- **Server**: `production`
- **Signature**: `def create_currency_prices(price_point_id: int, *, body: CreateCurrencyPricesRequest | CreateCurrencyPricesRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `price_point_id`
- **Params**: `price_point_id` — path · `body` — JSON body
- **Returns (parsed)**: `ComponentCurrencyPricesResponse`
- **Returns (raw)**: `ApiResult[ComponentCurrencyPricesResponse, CreateCurrencyPricesErrorBody]`
- **Error**: `CreateCurrencyPricesErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorArrayMapResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateCurrencyPricesRequest` | `maxio_advanced_billing/models/create_currency_prices_request.py` |
| `CreateCurrencyPricesRequestDict` | `maxio_advanced_billing/models/create_currency_prices_request.py` |
| `ComponentCurrencyPricesResponse` | `maxio_advanced_billing/models/component_currency_prices_response.py` |
| `CreateCurrencyPricesErrorBody` | `maxio_advanced_billing/errors/create_currency_prices_error.py` |
| `ErrorArrayMapResponse1` | `maxio_advanced_billing/models/error_array_map_response1.py` |

### client.component_price_points.list_all_component_price_points

- **Route**: `GET /components_price_points.json`
- **Server**: `production`
- **Signature**: `def list_all_component_price_points(*, include: ListComponentsPricePointsIncludeOrStr | None = None, page: int | None = 1, per_page: int | None = 20, direction: SortingDirectionOrStr | None = None, filter: ListPricePointsFilter | ListPricePointsFilterDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `include` — query · `page` — query · `per_page` — query · `direction` — query · `filter` — query
- **Returns (parsed)**: `ListComponentsPricePointsResponse`
- **Returns (raw)**: `ApiResult[ListComponentsPricePointsResponse, ListAllComponentPricePointsErrorBody]`
- **Error**: `ListAllComponentPricePointsErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ListComponentsPricePointsIncludeOrStr` | `maxio_advanced_billing/models/enums/list_components_price_points_include.py` |
| `SortingDirectionOrStr` | `maxio_advanced_billing/models/enums/sorting_direction.py` |
| `ListPricePointsFilter` | `maxio_advanced_billing/models/list_price_points_filter.py` |
| `ListPricePointsFilterDict` | `maxio_advanced_billing/models/list_price_points_filter.py` |
| `ListComponentsPricePointsResponse` | `maxio_advanced_billing/models/list_components_price_points_response.py` |
| `ListAllComponentPricePointsErrorBody` | `maxio_advanced_billing/errors/list_all_component_price_points_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.component_price_points.list_component_price_points

- **Route**: `GET /components/{component_id}/price_points.json`
- **Server**: `production`
- **Signature**: `def list_component_price_points(component_id: int, *, currency_prices: bool | None = None, page: int | None = 1, per_page: int | None = 20, filter_type: list[PricePointTypeOrStr] | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `component_id`
- **Params**: `component_id` — path · `currency_prices` — query · `page` — query · `per_page` — query · `filter_type` — query `filter[type]`
- **Returns (parsed)**: `ComponentPricePointsResponse`
- **Returns (raw)**: `ApiResult[ComponentPricePointsResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `PricePointTypeOrStr` | `maxio_advanced_billing/models/enums/price_point_type.py` |
| `ComponentPricePointsResponse` | `maxio_advanced_billing/models/component_price_points_response.py` |

### client.component_price_points.promote_component_price_point_to_default

- **Route**: `PUT /components/{component_id}/price_points/{price_point_id}/default.json`
- **Server**: `production`
- **Signature**: `def promote_component_price_point_to_default(component_id: int, price_point_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `component_id`, `price_point_id`
- **Params**: `component_id` — path · `price_point_id` — path
- **Returns (parsed)**: `ComponentResponse`
- **Returns (raw)**: `ApiResult[ComponentResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ComponentResponse` | `maxio_advanced_billing/models/component_response.py` |

### client.component_price_points.read_component_price_point

- **Route**: `GET /components/{component_id}/price_points/{price_point_id}.json`
- **Server**: `production`
- **Signature**: `def read_component_price_point(component_id: ComponentIdModel | ComponentIdModelDict, price_point_id: PricePointIdModel | PricePointIdModelDict, *, currency_prices: bool | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `component_id`, `price_point_id`
- **Params**: `component_id` — path · `price_point_id` — path · `currency_prices` — query
- **Returns (parsed)**: `ComponentPricePointCurrencyOverageResponse`
- **Returns (raw)**: `ApiResult[ComponentPricePointCurrencyOverageResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ComponentIdModel` | `maxio_advanced_billing/models/unions/component_id_model.py` |
| `ComponentIdModelDict` | `maxio_advanced_billing/models/unions/component_id_model.py` |
| `PricePointIdModel` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `PricePointIdModelDict` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `ComponentPricePointCurrencyOverageResponse` | `maxio_advanced_billing/models/component_price_point_currency_overage_response.py` |

### client.component_price_points.unarchive_component_price_point

- **Route**: `PUT /components/{component_id}/price_points/{price_point_id}/unarchive.json`
- **Server**: `production`
- **Signature**: `def unarchive_component_price_point(component_id: int, price_point_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `component_id`, `price_point_id`
- **Params**: `component_id` — path · `price_point_id` — path
- **Returns (parsed)**: `ComponentPricePointResponse`
- **Returns (raw)**: `ApiResult[ComponentPricePointResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ComponentPricePointResponse` | `maxio_advanced_billing/models/component_price_point_response.py` |

### client.component_price_points.update_component_price_point

- **Route**: `PUT /components/{component_id}/price_points/{price_point_id}.json`
- **Server**: `production`
- **Signature**: `def update_component_price_point(component_id: ComponentIdModel | ComponentIdModelDict, price_point_id: PricePointIdModel | PricePointIdModelDict, *, body: UpdateComponentPricePointRequest | UpdateComponentPricePointRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `component_id`, `price_point_id`
- **Params**: `component_id` — path · `price_point_id` — path · `body` — JSON body
- **Returns (parsed)**: `ComponentPricePointResponse`
- **Returns (raw)**: `ApiResult[ComponentPricePointResponse, UpdateComponentPricePointErrorBody]`
- **Error**: `UpdateComponentPricePointErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorArrayMapResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ComponentIdModel` | `maxio_advanced_billing/models/unions/component_id_model.py` |
| `ComponentIdModelDict` | `maxio_advanced_billing/models/unions/component_id_model.py` |
| `PricePointIdModel` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `PricePointIdModelDict` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `UpdateComponentPricePointRequest` | `maxio_advanced_billing/models/update_component_price_point_request.py` |
| `UpdateComponentPricePointRequestDict` | `maxio_advanced_billing/models/update_component_price_point_request.py` |
| `ComponentPricePointResponse` | `maxio_advanced_billing/models/component_price_point_response.py` |
| `UpdateComponentPricePointErrorBody` | `maxio_advanced_billing/errors/update_component_price_point_error.py` |
| `ErrorArrayMapResponse1` | `maxio_advanced_billing/models/error_array_map_response1.py` |

### client.component_price_points.update_currency_prices

- **Route**: `PUT /price_points/{price_point_id}/currency_prices.json`
- **Server**: `production`
- **Signature**: `def update_currency_prices(price_point_id: int, *, body: UpdateCurrencyPricesRequest | UpdateCurrencyPricesRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `price_point_id`
- **Params**: `price_point_id` — path · `body` — JSON body
- **Returns (parsed)**: `ComponentCurrencyPricesResponse`
- **Returns (raw)**: `ApiResult[ComponentCurrencyPricesResponse, UpdateCurrencyPricesErrorBody]`
- **Error**: `UpdateCurrencyPricesErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorArrayMapResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `UpdateCurrencyPricesRequest` | `maxio_advanced_billing/models/update_currency_prices_request.py` |
| `UpdateCurrencyPricesRequestDict` | `maxio_advanced_billing/models/update_currency_prices_request.py` |
| `ComponentCurrencyPricesResponse` | `maxio_advanced_billing/models/component_currency_prices_response.py` |
| `UpdateCurrencyPricesErrorBody` | `maxio_advanced_billing/errors/update_currency_prices_error.py` |
| `ErrorArrayMapResponse1` | `maxio_advanced_billing/models/error_array_map_response1.py` |

