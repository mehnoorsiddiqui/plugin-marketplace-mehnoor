<!-- Generated file — do not edit; regenerated with the SDK. -->

# Coupons — operations

Accessor: `client.coupons` · Source: `maxio_advanced_billing/apis/coupons.py` · 14 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.coupons.archive_coupon

- **Route**: `DELETE /product_families/{product_family_id}/coupons/{coupon_id}.json`
- **Server**: `production`
- **Signature**: `def archive_coupon(product_family_id: int, coupon_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_family_id`, `coupon_id`
- **Params**: `product_family_id` — path · `coupon_id` — path
- **Returns (parsed)**: `CouponResponse`
- **Returns (raw)**: `ApiResult[CouponResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `CouponResponse` | `maxio_advanced_billing/models/coupon_response.py` |

### client.coupons.create_coupon

- **Route**: `POST /product_families/{product_family_id}/coupons.json`
- **Server**: `production`
- **Signature**: `def create_coupon(product_family_id: int, *, body: CouponRequest | CouponRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_family_id`
- **Params**: `product_family_id` — path · `body` — JSON body
- **Returns (parsed)**: `CouponResponse`
- **Returns (raw)**: `ApiResult[CouponResponse, CreateCouponErrorBody]`
- **Error**: `CreateCouponErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CouponRequest` | `maxio_advanced_billing/models/coupon_request.py` |
| `CouponRequestDict` | `maxio_advanced_billing/models/coupon_request.py` |
| `CouponResponse` | `maxio_advanced_billing/models/coupon_response.py` |
| `CreateCouponErrorBody` | `maxio_advanced_billing/errors/create_coupon_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.coupons.create_coupon_subcodes

- **Route**: `POST /coupons/{coupon_id}/codes.json`
- **Server**: `production`
- **Signature**: `def create_coupon_subcodes(coupon_id: int, *, body: CouponSubcodes | CouponSubcodesDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `coupon_id`
- **Params**: `coupon_id` — path · `body` — JSON body
- **Returns (parsed)**: `CouponSubcodesResponse`
- **Returns (raw)**: `ApiResult[CouponSubcodesResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `CouponSubcodes` | `maxio_advanced_billing/models/coupon_subcodes.py` |
| `CouponSubcodesDict` | `maxio_advanced_billing/models/coupon_subcodes.py` |
| `CouponSubcodesResponse` | `maxio_advanced_billing/models/coupon_subcodes_response.py` |

### client.coupons.create_or_update_coupon_currency_prices

- **Route**: `PUT /coupons/{coupon_id}/currency_prices.json`
- **Server**: `production`
- **Signature**: `def create_or_update_coupon_currency_prices(coupon_id: int, *, body: CouponCurrencyRequest | CouponCurrencyRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `coupon_id`
- **Params**: `coupon_id` — path · `body` — JSON body
- **Returns (parsed)**: `CouponCurrencyResponse`
- **Returns (raw)**: `ApiResult[CouponCurrencyResponse, CreateOrUpdateCouponCurrencyPricesErrorBody]`
- **Error**: `CreateOrUpdateCouponCurrencyPricesErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorStringMapResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CouponCurrencyRequest` | `maxio_advanced_billing/models/coupon_currency_request.py` |
| `CouponCurrencyRequestDict` | `maxio_advanced_billing/models/coupon_currency_request.py` |
| `CouponCurrencyResponse` | `maxio_advanced_billing/models/coupon_currency_response.py` |
| `CreateOrUpdateCouponCurrencyPricesErrorBody` | `maxio_advanced_billing/errors/create_or_update_coupon_currency_prices_error.py` |
| `ErrorStringMapResponse1` | `maxio_advanced_billing/models/error_string_map_response1.py` |

### client.coupons.delete_coupon_subcode

- **Route**: `DELETE /coupons/{coupon_id}/codes/{subcode}.json`
- **Server**: `production`
- **Signature**: `def delete_coupon_subcode(coupon_id: int, subcode: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `coupon_id`, `subcode`
- **Params**: `coupon_id` — path · `subcode` — path
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, DeleteCouponSubcodeErrorBody]`
- **Error**: `DeleteCouponSubcodeErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `DeleteCouponSubcodeErrorBody` | `maxio_advanced_billing/errors/delete_coupon_subcode_error.py` |

### client.coupons.find_coupon

- **Route**: `GET /coupons/find.json`
- **Server**: `production`
- **Signature**: `def find_coupon(*, product_family_id: int | None = None, code: str | None = None, currency_prices: bool | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `product_family_id` — query · `code` — query · `currency_prices` — query
- **Returns (parsed)**: `CouponResponse`
- **Returns (raw)**: `ApiResult[CouponResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `CouponResponse` | `maxio_advanced_billing/models/coupon_response.py` |

### client.coupons.list_coupon_subcodes

- **Route**: `GET /coupons/{coupon_id}/codes.json`
- **Server**: `production`
- **Signature**: `def list_coupon_subcodes(coupon_id: int, *, page: int | None = 1, per_page: int | None = 20, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `coupon_id`
- **Params**: `coupon_id` — path · `page` — query · `per_page` — query
- **Returns (parsed)**: `CouponSubcodes`
- **Returns (raw)**: `ApiResult[CouponSubcodes, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `CouponSubcodes` | `maxio_advanced_billing/models/coupon_subcodes.py` |

### client.coupons.list_coupons

- **Route**: `GET /coupons.json`
- **Server**: `production`
- **Signature**: `def list_coupons(*, page: int | None = 1, per_page: int | None = 30, filter: ListCouponsFilter | ListCouponsFilterDict | None = None, currency_prices: bool | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `page` — query · `per_page` — query · `filter` — query · `currency_prices` — query
- **Returns (parsed)**: `list[CouponResponse]`
- **Returns (raw)**: `ApiResult[list[CouponResponse], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ListCouponsFilter` | `maxio_advanced_billing/models/list_coupons_filter.py` |
| `ListCouponsFilterDict` | `maxio_advanced_billing/models/list_coupons_filter.py` |
| `CouponResponse` | `maxio_advanced_billing/models/coupon_response.py` |

### client.coupons.list_coupons_for_product_family

- **Route**: `GET /product_families/{product_family_id}/coupons.json`
- **Server**: `production`
- **Signature**: `def list_coupons_for_product_family(product_family_id: int, *, page: int | None = 1, per_page: int | None = 30, filter: ListCouponsFilter | ListCouponsFilterDict | None = None, currency_prices: bool | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_family_id`
- **Params**: `product_family_id` — path · `page` — query · `per_page` — query · `filter` — query · `currency_prices` — query
- **Returns (parsed)**: `list[CouponResponse]`
- **Returns (raw)**: `ApiResult[list[CouponResponse], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ListCouponsFilter` | `maxio_advanced_billing/models/list_coupons_filter.py` |
| `ListCouponsFilterDict` | `maxio_advanced_billing/models/list_coupons_filter.py` |
| `CouponResponse` | `maxio_advanced_billing/models/coupon_response.py` |

### client.coupons.read_coupon

- **Route**: `GET /product_families/{product_family_id}/coupons/{coupon_id}.json`
- **Server**: `production`
- **Signature**: `def read_coupon(product_family_id: int, coupon_id: int, *, currency_prices: bool | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_family_id`, `coupon_id`
- **Params**: `product_family_id` — path · `coupon_id` — path · `currency_prices` — query
- **Returns (parsed)**: `CouponResponse`
- **Returns (raw)**: `ApiResult[CouponResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `CouponResponse` | `maxio_advanced_billing/models/coupon_response.py` |

### client.coupons.read_coupon_usage

- **Route**: `GET /product_families/{product_family_id}/coupons/{coupon_id}/usage.json`
- **Server**: `production`
- **Signature**: `def read_coupon_usage(product_family_id: int, coupon_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_family_id`, `coupon_id`
- **Params**: `product_family_id` — path · `coupon_id` — path
- **Returns (parsed)**: `list[CouponUsage]`
- **Returns (raw)**: `ApiResult[list[CouponUsage], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `CouponUsage` | `maxio_advanced_billing/models/coupon_usage.py` |

### client.coupons.update_coupon

- **Route**: `PUT /product_families/{product_family_id}/coupons/{coupon_id}.json`
- **Server**: `production`
- **Signature**: `def update_coupon(product_family_id: int, coupon_id: int, *, body: CouponRequest | CouponRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `product_family_id`, `coupon_id`
- **Params**: `product_family_id` — path · `coupon_id` — path · `body` — JSON body
- **Returns (parsed)**: `CouponResponse`
- **Returns (raw)**: `ApiResult[CouponResponse, UpdateCouponErrorBody]`
- **Error**: `UpdateCouponErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CouponRequest` | `maxio_advanced_billing/models/coupon_request.py` |
| `CouponRequestDict` | `maxio_advanced_billing/models/coupon_request.py` |
| `CouponResponse` | `maxio_advanced_billing/models/coupon_response.py` |
| `UpdateCouponErrorBody` | `maxio_advanced_billing/errors/update_coupon_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.coupons.update_coupon_subcodes

- **Route**: `PUT /coupons/{coupon_id}/codes.json`
- **Server**: `production`
- **Signature**: `def update_coupon_subcodes(coupon_id: int, *, body: CouponSubcodes | CouponSubcodesDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `coupon_id`
- **Params**: `coupon_id` — path · `body` — JSON body
- **Returns (parsed)**: `CouponSubcodesResponse`
- **Returns (raw)**: `ApiResult[CouponSubcodesResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `CouponSubcodes` | `maxio_advanced_billing/models/coupon_subcodes.py` |
| `CouponSubcodesDict` | `maxio_advanced_billing/models/coupon_subcodes.py` |
| `CouponSubcodesResponse` | `maxio_advanced_billing/models/coupon_subcodes_response.py` |

### client.coupons.validate_coupon

- **Route**: `GET /coupons/validate.json`
- **Server**: `production`
- **Signature**: `def validate_coupon(code: str, *, product_family_id: int | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `code`
- **Params**: `code` — query · `product_family_id` — query
- **Returns (parsed)**: `CouponResponse`
- **Returns (raw)**: `ApiResult[CouponResponse, ValidateCouponErrorBody]`
- **Error**: `ValidateCouponErrorBody` — **Case A (typed)**
- **Error arms**: `SingleStringErrorResponse1` [404] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CouponResponse` | `maxio_advanced_billing/models/coupon_response.py` |
| `ValidateCouponErrorBody` | `maxio_advanced_billing/errors/validate_coupon_error.py` |
| `SingleStringErrorResponse1` | `maxio_advanced_billing/models/single_string_error_response1.py` |

