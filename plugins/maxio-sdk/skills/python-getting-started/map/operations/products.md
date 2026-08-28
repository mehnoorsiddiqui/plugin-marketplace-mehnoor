<!-- Generated file — do not edit; regenerated with the SDK. -->

# Products — operations

Accessor: `client.products` · Source: `maxio_advanced_billing/apis/products.py` · 6 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.products.archive_product

- **Route**: `DELETE /products/{product_id}.json`
- **Server**: `production`
- **Signature**: `def archive_product(product_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_id`
- **Params**: `product_id` — path
- **Returns (parsed)**: `ProductResponse`
- **Returns (raw)**: `ApiResult[ProductResponse, ArchiveProductErrorBody]`
- **Error**: `ArchiveProductErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ProductResponse` | `maxio_advanced_billing/models/product_response.py` |
| `ArchiveProductErrorBody` | `maxio_advanced_billing/errors/archive_product_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.products.create_product

- **Route**: `POST /product_families/{product_family_id}/products.json`
- **Server**: `production`
- **Signature**: `def create_product(product_family_id: str, *, body: CreateOrUpdateProductRequest | CreateOrUpdateProductRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_family_id`
- **Params**: `product_family_id` — path · `body` — JSON body
- **Returns (parsed)**: `ProductResponse`
- **Returns (raw)**: `ApiResult[ProductResponse, CreateProductErrorBody]`
- **Error**: `CreateProductErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateOrUpdateProductRequest` | `maxio_advanced_billing/models/create_or_update_product_request.py` |
| `CreateOrUpdateProductRequestDict` | `maxio_advanced_billing/models/create_or_update_product_request.py` |
| `ProductResponse` | `maxio_advanced_billing/models/product_response.py` |
| `CreateProductErrorBody` | `maxio_advanced_billing/errors/create_product_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.products.list_products

- **Route**: `GET /products.json`
- **Server**: `production`
- **Signature**: `def list_products(*, date_field: BasicDateFieldOrStr | None = None, filter: ListProductsFilter | ListProductsFilterDict | None = None, end_date: Date | None = None, end_datetime: RFC3339DateTime | None = None, start_date: Date | None = None, start_datetime: RFC3339DateTime | None = None, page: int | None = 1, per_page: int | None = 20, include_archived: bool | None = None, include: ListProductsIncludeOrStr | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `date_field` — query · `filter` — query · `end_date` — query · `end_datetime` — query · `start_date` — query · `start_datetime` — query · `page` — query · `per_page` — query · `include_archived` — query · `include` — query
- **Returns (parsed)**: `list[ProductResponse]`
- **Returns (raw)**: `ApiResult[list[ProductResponse], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `BasicDateFieldOrStr` | `maxio_advanced_billing/models/enums/basic_date_field.py` |
| `ListProductsFilter` | `maxio_advanced_billing/models/list_products_filter.py` |
| `ListProductsFilterDict` | `maxio_advanced_billing/models/list_products_filter.py` |
| `ListProductsIncludeOrStr` | `maxio_advanced_billing/models/enums/list_products_include.py` |
| `ProductResponse` | `maxio_advanced_billing/models/product_response.py` |

### client.products.read_product

- **Route**: `GET /products/{product_id}.json`
- **Server**: `production`
- **Signature**: `def read_product(product_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_id`
- **Params**: `product_id` — path
- **Returns (parsed)**: `ProductResponse`
- **Returns (raw)**: `ApiResult[ProductResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ProductResponse` | `maxio_advanced_billing/models/product_response.py` |

### client.products.read_product_by_handle

- **Route**: `GET /products/handle/{api_handle}.json`
- **Server**: `production`
- **Signature**: `def read_product_by_handle(api_handle: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `api_handle`
- **Params**: `api_handle` — path
- **Returns (parsed)**: `ProductResponse`
- **Returns (raw)**: `ApiResult[ProductResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ProductResponse` | `maxio_advanced_billing/models/product_response.py` |

### client.products.update_product

- **Route**: `PUT /products/{product_id}.json`
- **Server**: `production`
- **Signature**: `def update_product(product_id: int, *, body: CreateOrUpdateProductRequest | CreateOrUpdateProductRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_id`
- **Params**: `product_id` — path · `body` — JSON body
- **Returns (parsed)**: `ProductResponse`
- **Returns (raw)**: `ApiResult[ProductResponse, UpdateProductErrorBody]`
- **Error**: `UpdateProductErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateOrUpdateProductRequest` | `maxio_advanced_billing/models/create_or_update_product_request.py` |
| `CreateOrUpdateProductRequestDict` | `maxio_advanced_billing/models/create_or_update_product_request.py` |
| `ProductResponse` | `maxio_advanced_billing/models/product_response.py` |
| `UpdateProductErrorBody` | `maxio_advanced_billing/errors/update_product_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

