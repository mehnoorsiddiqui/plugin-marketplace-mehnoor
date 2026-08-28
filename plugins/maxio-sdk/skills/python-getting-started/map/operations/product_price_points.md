<!-- Generated file — do not edit; regenerated with the SDK. -->

# ProductPricePoints — operations

Accessor: `client.product_price_points` · Source: `maxio_advanced_billing/apis/product_price_points.py` · 11 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.product_price_points.archive_product_price_point

- **Route**: `DELETE /products/{product_id}/price_points/{price_point_id}.json`
- **Server**: `production`
- **Signature**: `def archive_product_price_point(product_id: ProductIdModel | ProductIdModelDict, price_point_id: PricePointIdModel | PricePointIdModelDict, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_id`, `price_point_id`
- **Params**: `product_id` — path · `price_point_id` — path
- **Returns (parsed)**: `ProductPricePointResponse`
- **Returns (raw)**: `ApiResult[ProductPricePointResponse, ArchiveProductPricePointErrorBody]`
- **Error**: `ArchiveProductPricePointErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ProductIdModel` | `maxio_advanced_billing/models/unions/product_id_model.py` |
| `ProductIdModelDict` | `maxio_advanced_billing/models/unions/product_id_model.py` |
| `PricePointIdModel` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `PricePointIdModelDict` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `ProductPricePointResponse` | `maxio_advanced_billing/models/product_price_point_response.py` |
| `ArchiveProductPricePointErrorBody` | `maxio_advanced_billing/errors/archive_product_price_point_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.product_price_points.bulk_create_product_price_points

- **Route**: `POST /products/{product_id}/price_points/bulk.json`
- **Server**: `production`
- **Signature**: `def bulk_create_product_price_points(product_id: int, *, body: BulkCreateProductPricePointsRequest | BulkCreateProductPricePointsRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_id`
- **Params**: `product_id` — path · `body` — JSON body
- **Returns (parsed)**: `BulkCreateProductPricePointsResponse`
- **Returns (raw)**: `ApiResult[BulkCreateProductPricePointsResponse, BulkCreateProductPricePointsErrorBody]`
- **Error**: `BulkCreateProductPricePointsErrorBody` — **Case A (typed)**
- **Error arms**: `dict[str, Any]` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `BulkCreateProductPricePointsRequest` | `maxio_advanced_billing/models/bulk_create_product_price_points_request.py` |
| `BulkCreateProductPricePointsRequestDict` | `maxio_advanced_billing/models/bulk_create_product_price_points_request.py` |
| `BulkCreateProductPricePointsResponse` | `maxio_advanced_billing/models/bulk_create_product_price_points_response.py` |
| `BulkCreateProductPricePointsErrorBody` | `maxio_advanced_billing/errors/bulk_create_product_price_points_error.py` |

### client.product_price_points.create_product_currency_prices

- **Route**: `POST /product_price_points/{product_price_point_id}/currency_prices.json`
- **Server**: `production`
- **Signature**: `def create_product_currency_prices(product_price_point_id: int, *, body: CreateProductCurrencyPricesRequest | CreateProductCurrencyPricesRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_price_point_id`
- **Params**: `product_price_point_id` — path · `body` — JSON body
- **Returns (parsed)**: `CurrencyPricesResponse`
- **Returns (raw)**: `ApiResult[CurrencyPricesResponse, CreateProductCurrencyPricesErrorBody]`
- **Error**: `CreateProductCurrencyPricesErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorArrayMapResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateProductCurrencyPricesRequest` | `maxio_advanced_billing/models/create_product_currency_prices_request.py` |
| `CreateProductCurrencyPricesRequestDict` | `maxio_advanced_billing/models/create_product_currency_prices_request.py` |
| `CurrencyPricesResponse` | `maxio_advanced_billing/models/currency_prices_response.py` |
| `CreateProductCurrencyPricesErrorBody` | `maxio_advanced_billing/errors/create_product_currency_prices_error.py` |
| `ErrorArrayMapResponse1` | `maxio_advanced_billing/models/error_array_map_response1.py` |

### client.product_price_points.create_product_price_point

- **Route**: `POST /products/{product_id}/price_points.json`
- **Server**: `production`
- **Signature**: `def create_product_price_point(product_id: ProductIdModel | ProductIdModelDict, *, body: CreateProductPricePointRequest | CreateProductPricePointRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_id`
- **Params**: `product_id` — path · `body` — JSON body
- **Returns (parsed)**: `ProductPricePointResponse`
- **Returns (raw)**: `ApiResult[ProductPricePointResponse, CreateProductPricePointErrorBody]`
- **Error**: `CreateProductPricePointErrorBody` — **Case A (typed)**
- **Error arms**: `ProductPricePointErrorResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ProductIdModel` | `maxio_advanced_billing/models/unions/product_id_model.py` |
| `ProductIdModelDict` | `maxio_advanced_billing/models/unions/product_id_model.py` |
| `CreateProductPricePointRequest` | `maxio_advanced_billing/models/create_product_price_point_request.py` |
| `CreateProductPricePointRequestDict` | `maxio_advanced_billing/models/create_product_price_point_request.py` |
| `ProductPricePointResponse` | `maxio_advanced_billing/models/product_price_point_response.py` |
| `CreateProductPricePointErrorBody` | `maxio_advanced_billing/errors/create_product_price_point_error.py` |
| `ProductPricePointErrorResponse1` | `maxio_advanced_billing/models/product_price_point_error_response1.py` |

### client.product_price_points.list_all_product_price_points

- **Route**: `GET /products_price_points.json`
- **Server**: `production`
- **Signature**: `def list_all_product_price_points(*, direction: SortingDirectionOrStr | None = None, filter: ListPricePointsFilter | ListPricePointsFilterDict | None = None, include: ListProductsPricePointsIncludeOrStr | None = None, page: int | None = 1, per_page: int | None = 20, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `direction` — query · `filter` — query · `include` — query · `page` — query · `per_page` — query
- **Returns (parsed)**: `ListProductPricePointsResponse`
- **Returns (raw)**: `ApiResult[ListProductPricePointsResponse, ListAllProductPricePointsErrorBody]`
- **Error**: `ListAllProductPricePointsErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `SortingDirectionOrStr` | `maxio_advanced_billing/models/enums/sorting_direction.py` |
| `ListPricePointsFilter` | `maxio_advanced_billing/models/list_price_points_filter.py` |
| `ListPricePointsFilterDict` | `maxio_advanced_billing/models/list_price_points_filter.py` |
| `ListProductsPricePointsIncludeOrStr` | `maxio_advanced_billing/models/enums/list_products_price_points_include.py` |
| `ListProductPricePointsResponse` | `maxio_advanced_billing/models/list_product_price_points_response.py` |
| `ListAllProductPricePointsErrorBody` | `maxio_advanced_billing/errors/list_all_product_price_points_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.product_price_points.list_product_price_points

- **Route**: `GET /products/{product_id}/price_points.json`
- **Server**: `production`
- **Signature**: `def list_product_price_points(product_id: ProductIdModel | ProductIdModelDict, *, page: int | None = 1, per_page: int | None = 10, currency_prices: bool | None = None, filter_type: list[PricePointTypeOrStr] | None = None, archived: bool | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_id`
- **Params**: `product_id` — path · `page` — query · `per_page` — query · `currency_prices` — query · `filter_type` — query `filter[type]` · `archived` — query
- **Returns (parsed)**: `ListProductPricePointsResponse`
- **Returns (raw)**: `ApiResult[ListProductPricePointsResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ProductIdModel` | `maxio_advanced_billing/models/unions/product_id_model.py` |
| `ProductIdModelDict` | `maxio_advanced_billing/models/unions/product_id_model.py` |
| `PricePointTypeOrStr` | `maxio_advanced_billing/models/enums/price_point_type.py` |
| `ListProductPricePointsResponse` | `maxio_advanced_billing/models/list_product_price_points_response.py` |

### client.product_price_points.promote_product_price_point_to_default

- **Route**: `PATCH /products/{product_id}/price_points/{price_point_id}/default.json`
- **Server**: `production`
- **Signature**: `def promote_product_price_point_to_default(product_id: int, price_point_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_id`, `price_point_id`
- **Params**: `product_id` — path · `price_point_id` — path
- **Returns (parsed)**: `ProductResponse`
- **Returns (raw)**: `ApiResult[ProductResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ProductResponse` | `maxio_advanced_billing/models/product_response.py` |

### client.product_price_points.read_product_price_point

- **Route**: `GET /products/{product_id}/price_points/{price_point_id}.json`
- **Server**: `production`
- **Signature**: `def read_product_price_point(product_id: ProductIdModel | ProductIdModelDict, price_point_id: PricePointIdModel | PricePointIdModelDict, *, currency_prices: bool | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_id`, `price_point_id`
- **Params**: `product_id` — path · `price_point_id` — path · `currency_prices` — query
- **Returns (parsed)**: `ProductPricePointResponse`
- **Returns (raw)**: `ApiResult[ProductPricePointResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ProductIdModel` | `maxio_advanced_billing/models/unions/product_id_model.py` |
| `ProductIdModelDict` | `maxio_advanced_billing/models/unions/product_id_model.py` |
| `PricePointIdModel` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `PricePointIdModelDict` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `ProductPricePointResponse` | `maxio_advanced_billing/models/product_price_point_response.py` |

### client.product_price_points.unarchive_product_price_point

- **Route**: `PATCH /products/{product_id}/price_points/{price_point_id}/unarchive.json`
- **Server**: `production`
- **Signature**: `def unarchive_product_price_point(product_id: int, price_point_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_id`, `price_point_id`
- **Params**: `product_id` — path · `price_point_id` — path
- **Returns (parsed)**: `ProductPricePointResponse`
- **Returns (raw)**: `ApiResult[ProductPricePointResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ProductPricePointResponse` | `maxio_advanced_billing/models/product_price_point_response.py` |

### client.product_price_points.update_product_currency_prices

- **Route**: `PUT /product_price_points/{product_price_point_id}/currency_prices.json`
- **Server**: `production`
- **Signature**: `def update_product_currency_prices(product_price_point_id: int, *, body: UpdateCurrencyPricesRequest | UpdateCurrencyPricesRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_price_point_id`
- **Params**: `product_price_point_id` — path · `body` — JSON body
- **Returns (parsed)**: `CurrencyPricesResponse`
- **Returns (raw)**: `ApiResult[CurrencyPricesResponse, UpdateProductCurrencyPricesErrorBody]`
- **Error**: `UpdateProductCurrencyPricesErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorArrayMapResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `UpdateCurrencyPricesRequest` | `maxio_advanced_billing/models/update_currency_prices_request.py` |
| `UpdateCurrencyPricesRequestDict` | `maxio_advanced_billing/models/update_currency_prices_request.py` |
| `CurrencyPricesResponse` | `maxio_advanced_billing/models/currency_prices_response.py` |
| `UpdateProductCurrencyPricesErrorBody` | `maxio_advanced_billing/errors/update_product_currency_prices_error.py` |
| `ErrorArrayMapResponse1` | `maxio_advanced_billing/models/error_array_map_response1.py` |

### client.product_price_points.update_product_price_point

- **Route**: `PUT /products/{product_id}/price_points/{price_point_id}.json`
- **Server**: `production`
- **Signature**: `def update_product_price_point(product_id: ProductIdModel | ProductIdModelDict, price_point_id: PricePointIdModel | PricePointIdModelDict, *, body: UpdateProductPricePointRequest | UpdateProductPricePointRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_id`, `price_point_id`
- **Params**: `product_id` — path · `price_point_id` — path · `body` — JSON body
- **Returns (parsed)**: `ProductPricePointResponse`
- **Returns (raw)**: `ApiResult[ProductPricePointResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ProductIdModel` | `maxio_advanced_billing/models/unions/product_id_model.py` |
| `ProductIdModelDict` | `maxio_advanced_billing/models/unions/product_id_model.py` |
| `PricePointIdModel` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `PricePointIdModelDict` | `maxio_advanced_billing/models/unions/price_point_id_model.py` |
| `UpdateProductPricePointRequest` | `maxio_advanced_billing/models/update_product_price_point_request.py` |
| `UpdateProductPricePointRequestDict` | `maxio_advanced_billing/models/update_product_price_point_request.py` |
| `ProductPricePointResponse` | `maxio_advanced_billing/models/product_price_point_response.py` |

