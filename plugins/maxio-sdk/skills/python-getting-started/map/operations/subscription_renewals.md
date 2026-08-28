<!-- Generated file — do not edit; regenerated with the SDK. -->

# SubscriptionRenewals — operations

Accessor: `client.subscription_renewals` · Source: `maxio_advanced_billing/apis/subscription_renewals.py` · 11 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.subscription_renewals.cancel_scheduled_renewal_configuration

- **Route**: `PUT /subscriptions/{subscription_id}/scheduled_renewals/{id}/cancel.json`
- **Server**: `production`
- **Signature**: `def cancel_scheduled_renewal_configuration(subscription_id: int, id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `id`
- **Params**: `subscription_id` — path · `id` — path
- **Returns (parsed)**: `ScheduledRenewalConfigurationResponse`
- **Returns (raw)**: `ApiResult[ScheduledRenewalConfigurationResponse, CancelScheduledRenewalConfigurationErrorBody]`
- **Error**: `CancelScheduledRenewalConfigurationErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ScheduledRenewalConfigurationResponse` | `maxio_advanced_billing/models/scheduled_renewal_configuration_response.py` |
| `CancelScheduledRenewalConfigurationErrorBody` | `maxio_advanced_billing/errors/cancel_scheduled_renewal_configuration_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_renewals.create_scheduled_renewal_configuration

- **Route**: `POST /subscriptions/{subscription_id}/scheduled_renewals.json`
- **Server**: `production`
- **Signature**: `def create_scheduled_renewal_configuration(subscription_id: int, *, body: ScheduledRenewalConfigurationRequest | ScheduledRenewalConfigurationRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `ScheduledRenewalConfigurationResponse`
- **Returns (raw)**: `ApiResult[ScheduledRenewalConfigurationResponse, CreateScheduledRenewalConfigurationErrorBody]`
- **Error**: `CreateScheduledRenewalConfigurationErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ScheduledRenewalConfigurationRequest` | `maxio_advanced_billing/models/scheduled_renewal_configuration_request.py` |
| `ScheduledRenewalConfigurationRequestDict` | `maxio_advanced_billing/models/scheduled_renewal_configuration_request.py` |
| `ScheduledRenewalConfigurationResponse` | `maxio_advanced_billing/models/scheduled_renewal_configuration_response.py` |
| `CreateScheduledRenewalConfigurationErrorBody` | `maxio_advanced_billing/errors/create_scheduled_renewal_configuration_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_renewals.create_scheduled_renewal_configuration_item

- **Route**: `POST /subscriptions/{subscription_id}/scheduled_renewals/{scheduled_renewals_configuration_id}/configuration_items.json`
- **Server**: `production`
- **Signature**: `def create_scheduled_renewal_configuration_item(subscription_id: int, scheduled_renewals_configuration_id: int, *, body: ScheduledRenewalConfigurationItemRequest | ScheduledRenewalConfigurationItemRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `scheduled_renewals_configuration_id`
- **Params**: `subscription_id` — path · `scheduled_renewals_configuration_id` — path · `body` — JSON body
- **Returns (parsed)**: `ScheduledRenewalConfigurationItemResponse`
- **Returns (raw)**: `ApiResult[ScheduledRenewalConfigurationItemResponse, CreateScheduledRenewalConfigurationItemErrorBody]`
- **Error**: `CreateScheduledRenewalConfigurationItemErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ScheduledRenewalConfigurationItemRequest` | `maxio_advanced_billing/models/scheduled_renewal_configuration_item_request.py` |
| `ScheduledRenewalConfigurationItemRequestDict` | `maxio_advanced_billing/models/scheduled_renewal_configuration_item_request.py` |
| `ScheduledRenewalConfigurationItemResponse` | `maxio_advanced_billing/models/scheduled_renewal_configuration_item_response.py` |
| `CreateScheduledRenewalConfigurationItemErrorBody` | `maxio_advanced_billing/errors/create_scheduled_renewal_configuration_item_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_renewals.delete_scheduled_renewal_configuration_item

- **Route**: `DELETE /subscriptions/{subscription_id}/scheduled_renewals/{scheduled_renewals_configuration_id}/configuration_items/{id}.json`
- **Server**: `production`
- **Signature**: `def delete_scheduled_renewal_configuration_item(subscription_id: int, scheduled_renewals_configuration_id: int, id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `scheduled_renewals_configuration_id`, `id`
- **Params**: `subscription_id` — path · `scheduled_renewals_configuration_id` — path · `id` — path
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, DeleteScheduledRenewalConfigurationItemErrorBody]`
- **Error**: `DeleteScheduledRenewalConfigurationItemErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `DeleteScheduledRenewalConfigurationItemErrorBody` | `maxio_advanced_billing/errors/delete_scheduled_renewal_configuration_item_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_renewals.list_scheduled_renewal_configurations

- **Route**: `GET /subscriptions/{subscription_id}/scheduled_renewals.json`
- **Server**: `production`
- **Signature**: `def list_scheduled_renewal_configurations(subscription_id: int, *, status: StatusOrStr | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `status` — query
- **Returns (parsed)**: `ScheduledRenewalConfigurationsResponse`
- **Returns (raw)**: `ApiResult[ScheduledRenewalConfigurationsResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `StatusOrStr` | `maxio_advanced_billing/models/enums/status.py` |
| `ScheduledRenewalConfigurationsResponse` | `maxio_advanced_billing/models/scheduled_renewal_configurations_response.py` |

### client.subscription_renewals.lock_in_scheduled_renewal_immediately

- **Route**: `PUT /subscriptions/{subscription_id}/scheduled_renewals/{id}/immediate_lock_in.json`
- **Server**: `production`
- **Signature**: `def lock_in_scheduled_renewal_immediately(subscription_id: int, id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `id`
- **Params**: `subscription_id` — path · `id` — path
- **Returns (parsed)**: `ScheduledRenewalConfigurationResponse`
- **Returns (raw)**: `ApiResult[ScheduledRenewalConfigurationResponse, LockInScheduledRenewalImmediatelyErrorBody]`
- **Error**: `LockInScheduledRenewalImmediatelyErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ScheduledRenewalConfigurationResponse` | `maxio_advanced_billing/models/scheduled_renewal_configuration_response.py` |
| `LockInScheduledRenewalImmediatelyErrorBody` | `maxio_advanced_billing/errors/lock_in_scheduled_renewal_immediately_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_renewals.read_scheduled_renewal_configuration

- **Route**: `GET /subscriptions/{subscription_id}/scheduled_renewals/{id}.json`
- **Server**: `production`
- **Signature**: `def read_scheduled_renewal_configuration(subscription_id: int, id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `id`
- **Params**: `subscription_id` — path · `id` — path
- **Returns (parsed)**: `ScheduledRenewalConfigurationResponse`
- **Returns (raw)**: `ApiResult[ScheduledRenewalConfigurationResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ScheduledRenewalConfigurationResponse` | `maxio_advanced_billing/models/scheduled_renewal_configuration_response.py` |

### client.subscription_renewals.schedule_scheduled_renewal_lock_in

- **Route**: `PUT /subscriptions/{subscription_id}/scheduled_renewals/{id}/schedule_lock_in.json`
- **Server**: `production`
- **Signature**: `def schedule_scheduled_renewal_lock_in(subscription_id: int, id: int, *, body: ScheduledRenewalLockInRequest | ScheduledRenewalLockInRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `id`
- **Params**: `subscription_id` — path · `id` — path · `body` — JSON body
- **Returns (parsed)**: `ScheduledRenewalConfigurationResponse`
- **Returns (raw)**: `ApiResult[ScheduledRenewalConfigurationResponse, ScheduleScheduledRenewalLockInErrorBody]`
- **Error**: `ScheduleScheduledRenewalLockInErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ScheduledRenewalLockInRequest` | `maxio_advanced_billing/models/scheduled_renewal_lock_in_request.py` |
| `ScheduledRenewalLockInRequestDict` | `maxio_advanced_billing/models/scheduled_renewal_lock_in_request.py` |
| `ScheduledRenewalConfigurationResponse` | `maxio_advanced_billing/models/scheduled_renewal_configuration_response.py` |
| `ScheduleScheduledRenewalLockInErrorBody` | `maxio_advanced_billing/errors/schedule_scheduled_renewal_lock_in_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_renewals.unpublish_scheduled_renewal_configuration

- **Route**: `PUT /subscriptions/{subscription_id}/scheduled_renewals/{id}/unpublish.json`
- **Server**: `production`
- **Signature**: `def unpublish_scheduled_renewal_configuration(subscription_id: int, id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `id`
- **Params**: `subscription_id` — path · `id` — path
- **Returns (parsed)**: `ScheduledRenewalConfigurationResponse`
- **Returns (raw)**: `ApiResult[ScheduledRenewalConfigurationResponse, UnpublishScheduledRenewalConfigurationErrorBody]`
- **Error**: `UnpublishScheduledRenewalConfigurationErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ScheduledRenewalConfigurationResponse` | `maxio_advanced_billing/models/scheduled_renewal_configuration_response.py` |
| `UnpublishScheduledRenewalConfigurationErrorBody` | `maxio_advanced_billing/errors/unpublish_scheduled_renewal_configuration_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_renewals.update_scheduled_renewal_configuration

- **Route**: `PUT /subscriptions/{subscription_id}/scheduled_renewals/{id}.json`
- **Server**: `production`
- **Signature**: `def update_scheduled_renewal_configuration(subscription_id: int, id: int, *, body: ScheduledRenewalConfigurationRequest | ScheduledRenewalConfigurationRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `id`
- **Params**: `subscription_id` — path · `id` — path · `body` — JSON body
- **Returns (parsed)**: `ScheduledRenewalConfigurationResponse`
- **Returns (raw)**: `ApiResult[ScheduledRenewalConfigurationResponse, UpdateScheduledRenewalConfigurationErrorBody]`
- **Error**: `UpdateScheduledRenewalConfigurationErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ScheduledRenewalConfigurationRequest` | `maxio_advanced_billing/models/scheduled_renewal_configuration_request.py` |
| `ScheduledRenewalConfigurationRequestDict` | `maxio_advanced_billing/models/scheduled_renewal_configuration_request.py` |
| `ScheduledRenewalConfigurationResponse` | `maxio_advanced_billing/models/scheduled_renewal_configuration_response.py` |
| `UpdateScheduledRenewalConfigurationErrorBody` | `maxio_advanced_billing/errors/update_scheduled_renewal_configuration_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_renewals.update_scheduled_renewal_configuration_item

- **Route**: `PUT /subscriptions/{subscription_id}/scheduled_renewals/{scheduled_renewals_configuration_id}/configuration_items/{id}.json`
- **Server**: `production`
- **Signature**: `def update_scheduled_renewal_configuration_item(subscription_id: int, scheduled_renewals_configuration_id: int, id: int, *, body: ScheduledRenewalUpdateRequest | ScheduledRenewalUpdateRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `scheduled_renewals_configuration_id`, `id`
- **Params**: `subscription_id` — path · `scheduled_renewals_configuration_id` — path · `id` — path · `body` — JSON body
- **Returns (parsed)**: `ScheduledRenewalConfigurationItemResponse`
- **Returns (raw)**: `ApiResult[ScheduledRenewalConfigurationItemResponse, UpdateScheduledRenewalConfigurationItemErrorBody]`
- **Error**: `UpdateScheduledRenewalConfigurationItemErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ScheduledRenewalUpdateRequest` | `maxio_advanced_billing/models/scheduled_renewal_update_request.py` |
| `ScheduledRenewalUpdateRequestDict` | `maxio_advanced_billing/models/scheduled_renewal_update_request.py` |
| `ScheduledRenewalConfigurationItemResponse` | `maxio_advanced_billing/models/scheduled_renewal_configuration_item_response.py` |
| `UpdateScheduledRenewalConfigurationItemErrorBody` | `maxio_advanced_billing/errors/update_scheduled_renewal_configuration_item_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

