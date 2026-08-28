<!-- Generated file — do not edit; regenerated with the SDK. -->

# Subscriptions — operations

Accessor: `client.subscriptions` · Source: `maxio_advanced_billing/apis/subscriptions.py` · 12 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.subscriptions.activate_subscription

- **Route**: `PUT /subscriptions/{subscription_id}/activate.json`
- **Server**: `production`
- **Signature**: `def activate_subscription(subscription_id: int, *, body: ActivateSubscriptionRequest | ActivateSubscriptionRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, ActivateSubscriptionErrorBody]`
- **Error**: `ActivateSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorArrayMapResponse1` [400] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ActivateSubscriptionRequest` | `maxio_advanced_billing/models/activate_subscription_request.py` |
| `ActivateSubscriptionRequestDict` | `maxio_advanced_billing/models/activate_subscription_request.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `ActivateSubscriptionErrorBody` | `maxio_advanced_billing/errors/activate_subscription_error.py` |
| `ErrorArrayMapResponse1` | `maxio_advanced_billing/models/error_array_map_response1.py` |

### client.subscriptions.apply_coupons_to_subscription

- **Route**: `POST /subscriptions/{subscription_id}/add_coupon.json`
- **Server**: `production`
- **Signature**: `def apply_coupons_to_subscription(subscription_id: int, *, code: str | None = None, body: AddCouponsRequest | AddCouponsRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `code` — query · `body` — JSON body
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, ApplyCouponsToSubscriptionErrorBody]`
- **Error**: `ApplyCouponsToSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `SubscriptionAddCouponError1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `AddCouponsRequest` | `maxio_advanced_billing/models/add_coupons_request.py` |
| `AddCouponsRequestDict` | `maxio_advanced_billing/models/add_coupons_request.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `ApplyCouponsToSubscriptionErrorBody` | `maxio_advanced_billing/errors/apply_coupons_to_subscription_error.py` |
| `SubscriptionAddCouponError1` | `maxio_advanced_billing/models/subscription_add_coupon_error1.py` |

### client.subscriptions.create_subscription

- **Route**: `POST /subscriptions.json`
- **Server**: `production`
- **Signature**: `def create_subscription(*, body: CreateSubscriptionRequest | CreateSubscriptionRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `body` — JSON body
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, CreateSubscriptionErrorBody]`
- **Error**: `CreateSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateSubscriptionRequest` | `maxio_advanced_billing/models/create_subscription_request.py` |
| `CreateSubscriptionRequestDict` | `maxio_advanced_billing/models/create_subscription_request.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `CreateSubscriptionErrorBody` | `maxio_advanced_billing/errors/create_subscription_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscriptions.find_subscription

- **Route**: `GET /subscriptions/lookup.json`
- **Server**: `production`
- **Signature**: `def find_subscription(*, reference: str | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `reference` — query
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, FindSubscriptionErrorBody]`
- **Error**: `FindSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `FindSubscriptionErrorBody` | `maxio_advanced_billing/errors/find_subscription_error.py` |

### client.subscriptions.list_subscriptions

- **Route**: `GET /subscriptions.json`
- **Server**: `production`
- **Signature**: `def list_subscriptions(*, page: int | None = 1, per_page: int | None = 20, state: SubscriptionStateFilterOrStr | None = None, product: int | None = None, product_price_point_id: int | None = None, coupon: int | None = None, coupon_code: str | None = None, branding_theme_id: int | None = None, date_field: SubscriptionDateFieldOrStr | None = None, start_date: Date | None = None, end_date: Date | None = None, start_datetime: RFC3339DateTime | None = None, end_datetime: RFC3339DateTime | None = None, metadata: dict[str, str] | None = None, direction: SortingDirectionOrStr | None = None, sort: SubscriptionSortOrStr | None = None, include: list[SubscriptionListIncludeOrStr] | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `page` — query · `per_page` — query · `state` — query · `product` — query · `product_price_point_id` — query · `coupon` — query · `coupon_code` — query · `branding_theme_id` — query · `date_field` — query · `start_date` — query · `end_date` — query · `start_datetime` — query · `end_datetime` — query · `metadata` — query · `direction` — query · `sort` — query · `include` — query
- **Returns (parsed)**: `list[SubscriptionResponse]`
- **Returns (raw)**: `ApiResult[list[SubscriptionResponse], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SubscriptionStateFilterOrStr` | `maxio_advanced_billing/models/enums/subscription_state_filter.py` |
| `SubscriptionDateFieldOrStr` | `maxio_advanced_billing/models/enums/subscription_date_field.py` |
| `SortingDirectionOrStr` | `maxio_advanced_billing/models/enums/sorting_direction.py` |
| `SubscriptionSortOrStr` | `maxio_advanced_billing/models/enums/subscription_sort.py` |
| `SubscriptionListIncludeOrStr` | `maxio_advanced_billing/models/enums/subscription_list_include.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |

### client.subscriptions.override_subscription

- **Route**: `PUT /subscriptions/{subscription_id}/override.json`
- **Server**: `production`
- **Signature**: `def override_subscription(subscription_id: int, *, body: OverrideSubscriptionRequest | OverrideSubscriptionRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, OverrideSubscriptionErrorBody]`
- **Error**: `OverrideSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `SingleErrorResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `OverrideSubscriptionRequest` | `maxio_advanced_billing/models/override_subscription_request.py` |
| `OverrideSubscriptionRequestDict` | `maxio_advanced_billing/models/override_subscription_request.py` |
| `OverrideSubscriptionErrorBody` | `maxio_advanced_billing/errors/override_subscription_error.py` |
| `SingleErrorResponse1` | `maxio_advanced_billing/models/single_error_response1.py` |

### client.subscriptions.preview_subscription

- **Route**: `POST /subscriptions/preview.json`
- **Server**: `production`
- **Signature**: `def preview_subscription(*, body: CreateSubscriptionRequest | CreateSubscriptionRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `body` — JSON body
- **Returns (parsed)**: `SubscriptionPreviewResponse`
- **Returns (raw)**: `ApiResult[SubscriptionPreviewResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `CreateSubscriptionRequest` | `maxio_advanced_billing/models/create_subscription_request.py` |
| `CreateSubscriptionRequestDict` | `maxio_advanced_billing/models/create_subscription_request.py` |
| `SubscriptionPreviewResponse` | `maxio_advanced_billing/models/subscription_preview_response.py` |

### client.subscriptions.purge_subscription

- **Route**: `POST /subscriptions/{subscription_id}/purge.json`
- **Server**: `production`
- **Signature**: `def purge_subscription(subscription_id: int, ack: int, *, cascade: list[SubscriptionPurgeTypeOrStr] | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `ack`
- **Params**: `subscription_id` — path · `ack` — query · `cascade` — query
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, PurgeSubscriptionErrorBody]`
- **Error**: `PurgeSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `SubscriptionResponse` [400] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `SubscriptionPurgeTypeOrStr` | `maxio_advanced_billing/models/enums/subscription_purge_type.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `PurgeSubscriptionErrorBody` | `maxio_advanced_billing/errors/purge_subscription_error.py` |

### client.subscriptions.read_subscription

- **Route**: `GET /subscriptions/{subscription_id}.json`
- **Server**: `production`
- **Signature**: `def read_subscription(subscription_id: int, *, include: list[SubscriptionIncludeOrStr] | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `include` — query
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SubscriptionIncludeOrStr` | `maxio_advanced_billing/models/enums/subscription_include.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |

### client.subscriptions.remove_coupon_from_subscription

- **Route**: `DELETE /subscriptions/{subscription_id}/remove_coupon.json`
- **Server**: `production`
- **Signature**: `def remove_coupon_from_subscription(subscription_id: int, *, coupon_code: str | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `coupon_code` — query
- **Returns (parsed)**: `str`
- **Returns (raw)**: `ApiResult[str, RemoveCouponFromSubscriptionErrorBody]`
- **Error**: `RemoveCouponFromSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `SubscriptionRemoveCouponErrors1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `RemoveCouponFromSubscriptionErrorBody` | `maxio_advanced_billing/errors/remove_coupon_from_subscription_error.py` |
| `SubscriptionRemoveCouponErrors1` | `maxio_advanced_billing/models/subscription_remove_coupon_errors1.py` |

### client.subscriptions.update_prepaid_subscription_configuration

- **Route**: `POST /subscriptions/{subscription_id}/prepaid_configurations.json`
- **Server**: `production`
- **Signature**: `def update_prepaid_subscription_configuration(subscription_id: int, *, body: UpsertPrepaidConfigurationRequest | UpsertPrepaidConfigurationRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `PrepaidConfigurationResponse`
- **Returns (raw)**: `ApiResult[PrepaidConfigurationResponse, UpdatePrepaidSubscriptionConfigurationErrorBody]`
- **Error**: `UpdatePrepaidSubscriptionConfigurationErrorBody` — **Case A (typed)**
- **Error arms**: `PrepaidConfigurationErrorResponse` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `UpsertPrepaidConfigurationRequest` | `maxio_advanced_billing/models/upsert_prepaid_configuration_request.py` |
| `UpsertPrepaidConfigurationRequestDict` | `maxio_advanced_billing/models/upsert_prepaid_configuration_request.py` |
| `PrepaidConfigurationResponse` | `maxio_advanced_billing/models/prepaid_configuration_response.py` |
| `UpdatePrepaidSubscriptionConfigurationErrorBody` | `maxio_advanced_billing/errors/update_prepaid_subscription_configuration_error.py` |
| `PrepaidConfigurationErrorResponse` | `maxio_advanced_billing/models/unions/prepaid_configuration_error_response.py` |

### client.subscriptions.update_subscription

- **Route**: `PUT /subscriptions/{subscription_id}.json`
- **Server**: `production`
- **Signature**: `def update_subscription(subscription_id: int, *, body: UpdateSubscriptionRequest | UpdateSubscriptionRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, UpdateSubscriptionErrorBody]`
- **Error**: `UpdateSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `UpdateSubscriptionRequest` | `maxio_advanced_billing/models/update_subscription_request.py` |
| `UpdateSubscriptionRequestDict` | `maxio_advanced_billing/models/update_subscription_request.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `UpdateSubscriptionErrorBody` | `maxio_advanced_billing/errors/update_subscription_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

