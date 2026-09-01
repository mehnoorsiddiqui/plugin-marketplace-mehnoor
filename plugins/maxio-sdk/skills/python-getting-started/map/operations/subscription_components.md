<!-- Generated file — do not edit; regenerated with the SDK. -->

# SubscriptionComponents — operations

Accessor: `client.subscription_components` · Source: `maxio_advanced_billing/apis/subscription_components.py` · 17 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded, and an operation with no table mentions nothing but builtins and those.

### client.subscription_components.activate_event_based_component

- **Route**: `POST /event_based_billing/subscriptions/{subscription_id}/components/{component_id}/activate.json`
- **Server**: `production`
- **Signature**: `def activate_event_based_component(subscription_id: int, component_id: int, *, body: ActivateEventBasedComponent | ActivateEventBasedComponentDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `component_id`
- **Params**: `subscription_id` — path · `component_id` — path · `body` — JSON body
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ActivateEventBasedComponent` | `maxio_advanced_billing/models/activate_event_based_component.py` |
| `ActivateEventBasedComponentDict` | `maxio_advanced_billing/models/activate_event_based_component.py` |

### client.subscription_components.allocate_component

- **Route**: `POST /subscriptions/{subscription_id}/components/{component_id}/allocations.json`
- **Server**: `production`
- **Signature**: `def allocate_component(subscription_id: int, component_id: int, *, body: CreateAllocationRequest | CreateAllocationRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `component_id`
- **Params**: `subscription_id` — path · `component_id` — path · `body` — JSON body
- **Returns (parsed)**: `AllocationResponse`
- **Returns (raw)**: `ApiResult[AllocationResponse, AllocateComponentErrorBody]`
- **Error**: `AllocateComponentErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateAllocationRequest` | `maxio_advanced_billing/models/create_allocation_request.py` |
| `CreateAllocationRequestDict` | `maxio_advanced_billing/models/create_allocation_request.py` |
| `AllocationResponse` | `maxio_advanced_billing/models/allocation_response.py` |
| `AllocateComponentErrorBody` | `maxio_advanced_billing/errors/allocate_component_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_components.allocate_components

- **Route**: `POST /subscriptions/{subscription_id}/allocations.json`
- **Server**: `production`
- **Signature**: `def allocate_components(subscription_id: int, *, body: AllocateComponents | AllocateComponentsDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `list[AllocationResponse]`
- **Returns (raw)**: `ApiResult[list[AllocationResponse], AllocateComponentsErrorBody]`
- **Error**: `AllocateComponentsErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `AllocateComponents` | `maxio_advanced_billing/models/allocate_components.py` |
| `AllocateComponentsDict` | `maxio_advanced_billing/models/allocate_components.py` |
| `AllocationResponse` | `maxio_advanced_billing/models/allocation_response.py` |
| `AllocateComponentsErrorBody` | `maxio_advanced_billing/errors/allocate_components_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_components.bulk_record_events

- **Route**: `POST /events/{api_handle}/bulk.json`
- **Server**: `ebb`
- **Signature**: `def bulk_record_events(api_handle: str, *, store_uid: str | None = None, body: list[EbbEvent | EbbEventDict] | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `api_handle`
- **Params**: `api_handle` — path · `store_uid` — query · `body` — JSON body
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `EbbEvent` | `maxio_advanced_billing/models/ebb_event.py` |
| `EbbEventDict` | `maxio_advanced_billing/models/ebb_event.py` |

### client.subscription_components.bulk_reset_subscription_components_price_points

- **Route**: `POST /subscriptions/{subscription_id}/price_points/reset.json`
- **Server**: `production`
- **Signature**: `def bulk_reset_subscription_components_price_points(subscription_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |

### client.subscription_components.bulk_update_subscription_components_price_points

- **Route**: `POST /subscriptions/{subscription_id}/price_points.json`
- **Server**: `production`
- **Signature**: `def bulk_update_subscription_components_price_points(subscription_id: int, *, body: BulkComponentsPricePointAssignment | BulkComponentsPricePointAssignmentDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `BulkComponentsPricePointAssignment`
- **Returns (raw)**: `ApiResult[BulkComponentsPricePointAssignment, BulkUpdateSubscriptionComponentsPricePointsErrorBody]`
- **Error**: `BulkUpdateSubscriptionComponentsPricePointsErrorBody` — **Case A (typed)**
- **Error arms**: `ComponentPricePointError1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `BulkComponentsPricePointAssignment` | `maxio_advanced_billing/models/bulk_components_price_point_assignment.py` |
| `BulkComponentsPricePointAssignmentDict` | `maxio_advanced_billing/models/bulk_components_price_point_assignment.py` |
| `BulkUpdateSubscriptionComponentsPricePointsErrorBody` | `maxio_advanced_billing/errors/bulk_update_subscription_components_price_points_error.py` |
| `ComponentPricePointError1` | `maxio_advanced_billing/models/component_price_point_error1.py` |

### client.subscription_components.create_usage

- **Route**: `POST /subscriptions/{subscription_id_or_reference}/components/{component_id}/usages.json`
- **Server**: `production`
- **Signature**: `def create_usage(subscription_id_or_reference: SubscriptionIdOrReference | SubscriptionIdOrReferenceDict, component_id: ComponentIdModel | ComponentIdModelDict, *, body: CreateUsageRequest | CreateUsageRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id_or_reference`, `component_id`
- **Params**: `subscription_id_or_reference` — path · `component_id` — path · `body` — JSON body
- **Returns (parsed)**: `UsageResponse`
- **Returns (raw)**: `ApiResult[UsageResponse, CreateUsageErrorBody]`
- **Error**: `CreateUsageErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `SubscriptionIdOrReference` | `maxio_advanced_billing/models/unions/subscription_id_or_reference.py` |
| `SubscriptionIdOrReferenceDict` | `maxio_advanced_billing/models/unions/subscription_id_or_reference.py` |
| `ComponentIdModel` | `maxio_advanced_billing/models/unions/component_id_model.py` |
| `ComponentIdModelDict` | `maxio_advanced_billing/models/unions/component_id_model.py` |
| `CreateUsageRequest` | `maxio_advanced_billing/models/create_usage_request.py` |
| `CreateUsageRequestDict` | `maxio_advanced_billing/models/create_usage_request.py` |
| `UsageResponse` | `maxio_advanced_billing/models/usage_response.py` |
| `CreateUsageErrorBody` | `maxio_advanced_billing/errors/create_usage_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_components.deactivate_event_based_component

- **Route**: `POST /event_based_billing/subscriptions/{subscription_id}/components/{component_id}/deactivate.json`
- **Server**: `production`
- **Signature**: `def deactivate_event_based_component(subscription_id: int, component_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `component_id`
- **Params**: `subscription_id` — path · `component_id` — path
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, RawError]`
- **Error**: `RawError` — **Case B**

### client.subscription_components.delete_prepaid_usage_allocation

- **Route**: `DELETE /subscriptions/{subscription_id}/components/{component_id}/allocations/{allocation_id}.json`
- **Server**: `production`
- **Signature**: `def delete_prepaid_usage_allocation(subscription_id: int, component_id: int, allocation_id: int, *, body: CreditSchemeRequest | CreditSchemeRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `component_id`, `allocation_id`
- **Params**: `subscription_id` — path · `component_id` — path · `allocation_id` — path · `body` — JSON body
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, DeletePrepaidUsageAllocationErrorBody]`
- **Error**: `DeletePrepaidUsageAllocationErrorBody` — **Case A (typed)**
- **Error arms**: `SubscriptionComponentAllocationError1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `CreditSchemeRequest` | `maxio_advanced_billing/models/credit_scheme_request.py` |
| `CreditSchemeRequestDict` | `maxio_advanced_billing/models/credit_scheme_request.py` |
| `DeletePrepaidUsageAllocationErrorBody` | `maxio_advanced_billing/errors/delete_prepaid_usage_allocation_error.py` |
| `SubscriptionComponentAllocationError1` | `maxio_advanced_billing/models/subscription_component_allocation_error1.py` |

### client.subscription_components.list_allocations

- **Route**: `GET /subscriptions/{subscription_id}/components/{component_id}/allocations.json`
- **Server**: `production`
- **Signature**: `def list_allocations(subscription_id: int, component_id: int, *, page: int | None = 1, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `component_id`
- **Params**: `subscription_id` — path · `component_id` — path · `page` — query
- **Returns (parsed)**: `list[AllocationResponse]`
- **Returns (raw)**: `ApiResult[list[AllocationResponse], ListAllocationsErrorBody]`
- **Error**: `ListAllocationsErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `AllocationResponse` | `maxio_advanced_billing/models/allocation_response.py` |
| `ListAllocationsErrorBody` | `maxio_advanced_billing/errors/list_allocations_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_components.list_subscription_components

- **Route**: `GET /subscriptions/{subscription_id}/components.json`
- **Server**: `production`
- **Signature**: `def list_subscription_components(subscription_id: int, *, date_field: SubscriptionListDateFieldOrStr | None = None, direction: SortingDirectionOrStr | None = None, filter: ListSubscriptionComponentsFilter | ListSubscriptionComponentsFilterDict | None = None, end_date: str | None = None, end_datetime: str | None = None, price_point_ids: IncludeNotNullOrStr | None = None, product_family_ids: list[int] | None = None, sort: ListSubscriptionComponentsSortOrStr | None = None, start_date: str | None = None, start_datetime: str | None = None, include: list[ListSubscriptionComponentsIncludeOrStr] | None = None, in_use: bool | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `date_field` — query · `direction` — query · `filter` — query · `end_date` — query · `end_datetime` — query · `price_point_ids` — query · `product_family_ids` — query · `sort` — query · `start_date` — query · `start_datetime` — query · `include` — query · `in_use` — query
- **Returns (parsed)**: `list[SubscriptionComponentResponse]`
- **Returns (raw)**: `ApiResult[list[SubscriptionComponentResponse], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SubscriptionListDateFieldOrStr` | `maxio_advanced_billing/models/enums/subscription_list_date_field.py` |
| `SortingDirectionOrStr` | `maxio_advanced_billing/models/enums/sorting_direction.py` |
| `ListSubscriptionComponentsFilter` | `maxio_advanced_billing/models/list_subscription_components_filter.py` |
| `ListSubscriptionComponentsFilterDict` | `maxio_advanced_billing/models/list_subscription_components_filter.py` |
| `IncludeNotNullOrStr` | `maxio_advanced_billing/models/enums/include_not_null.py` |
| `ListSubscriptionComponentsSortOrStr` | `maxio_advanced_billing/models/enums/list_subscription_components_sort.py` |
| `ListSubscriptionComponentsIncludeOrStr` | `maxio_advanced_billing/models/enums/list_subscription_components_include.py` |
| `SubscriptionComponentResponse` | `maxio_advanced_billing/models/subscription_component_response.py` |

### client.subscription_components.list_subscription_components_for_site

- **Route**: `GET /subscriptions_components.json`
- **Server**: `production`
- **Signature**: `def list_subscription_components_for_site(*, page: int | None = 1, per_page: int | None = 20, sort: ListSubscriptionComponentsSortOrStr | None = None, direction: SortingDirectionOrStr | None = None, filter: ListSubscriptionComponentsForSiteFilter | ListSubscriptionComponentsForSiteFilterDict | None = None, date_field: SubscriptionListDateFieldOrStr | None = None, start_date: str | None = None, start_datetime: str | None = None, end_date: str | None = None, end_datetime: str | None = None, subscription_ids: list[int] | None = None, price_point_ids: IncludeNotNullOrStr | None = None, product_family_ids: list[int] | None = None, include: ListSubscriptionComponentsIncludeOrStr | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `page` — query · `per_page` — query · `sort` — query · `direction` — query · `filter` — query · `date_field` — query · `start_date` — query · `start_datetime` — query · `end_date` — query · `end_datetime` — query · `subscription_ids` — query · `price_point_ids` — query · `product_family_ids` — query · `include` — query
- **Returns (parsed)**: `ListSubscriptionComponentsResponse`
- **Returns (raw)**: `ApiResult[ListSubscriptionComponentsResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ListSubscriptionComponentsSortOrStr` | `maxio_advanced_billing/models/enums/list_subscription_components_sort.py` |
| `SortingDirectionOrStr` | `maxio_advanced_billing/models/enums/sorting_direction.py` |
| `ListSubscriptionComponentsForSiteFilter` | `maxio_advanced_billing/models/list_subscription_components_for_site_filter.py` |
| `ListSubscriptionComponentsForSiteFilterDict` | `maxio_advanced_billing/models/list_subscription_components_for_site_filter.py` |
| `SubscriptionListDateFieldOrStr` | `maxio_advanced_billing/models/enums/subscription_list_date_field.py` |
| `IncludeNotNullOrStr` | `maxio_advanced_billing/models/enums/include_not_null.py` |
| `ListSubscriptionComponentsIncludeOrStr` | `maxio_advanced_billing/models/enums/list_subscription_components_include.py` |
| `ListSubscriptionComponentsResponse` | `maxio_advanced_billing/models/list_subscription_components_response.py` |

### client.subscription_components.list_usages

- **Route**: `GET /subscriptions/{subscription_id_or_reference}/components/{component_id}/usages.json`
- **Server**: `production`
- **Signature**: `def list_usages(subscription_id_or_reference: SubscriptionIdOrReference | SubscriptionIdOrReferenceDict, component_id: ComponentIdModel | ComponentIdModelDict, *, since_id: int | None = None, max_id: int | None = None, since_date: Date | None = None, until_date: Date | None = None, page: int | None = 1, per_page: int | None = 20, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id_or_reference`, `component_id`
- **Params**: `subscription_id_or_reference` — path · `component_id` — path · `since_id` — query · `max_id` — query · `since_date` — query · `until_date` — query · `page` — query · `per_page` — query
- **Returns (parsed)**: `list[UsageResponse]`
- **Returns (raw)**: `ApiResult[list[UsageResponse], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SubscriptionIdOrReference` | `maxio_advanced_billing/models/unions/subscription_id_or_reference.py` |
| `SubscriptionIdOrReferenceDict` | `maxio_advanced_billing/models/unions/subscription_id_or_reference.py` |
| `ComponentIdModel` | `maxio_advanced_billing/models/unions/component_id_model.py` |
| `ComponentIdModelDict` | `maxio_advanced_billing/models/unions/component_id_model.py` |
| `UsageResponse` | `maxio_advanced_billing/models/usage_response.py` |

### client.subscription_components.preview_allocations

- **Route**: `POST /subscriptions/{subscription_id}/allocations/preview.json`
- **Server**: `production`
- **Signature**: `def preview_allocations(subscription_id: int, *, body: PreviewAllocationsRequest | PreviewAllocationsRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `AllocationPreviewResponse`
- **Returns (raw)**: `ApiResult[AllocationPreviewResponse, PreviewAllocationsErrorBody]`
- **Error**: `PreviewAllocationsErrorBody` — **Case A (typed)**
- **Error arms**: `ComponentAllocationError1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `PreviewAllocationsRequest` | `maxio_advanced_billing/models/preview_allocations_request.py` |
| `PreviewAllocationsRequestDict` | `maxio_advanced_billing/models/preview_allocations_request.py` |
| `AllocationPreviewResponse` | `maxio_advanced_billing/models/allocation_preview_response.py` |
| `PreviewAllocationsErrorBody` | `maxio_advanced_billing/errors/preview_allocations_error.py` |
| `ComponentAllocationError1` | `maxio_advanced_billing/models/component_allocation_error1.py` |

### client.subscription_components.read_subscription_component

- **Route**: `GET /subscriptions/{subscription_id}/components/{component_id}.json`
- **Server**: `production`
- **Signature**: `def read_subscription_component(subscription_id: int, component_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `component_id`
- **Params**: `subscription_id` — path · `component_id` — path
- **Returns (parsed)**: `SubscriptionComponentResponse`
- **Returns (raw)**: `ApiResult[SubscriptionComponentResponse, ReadSubscriptionComponentErrorBody]`
- **Error**: `ReadSubscriptionComponentErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `SubscriptionComponentResponse` | `maxio_advanced_billing/models/subscription_component_response.py` |
| `ReadSubscriptionComponentErrorBody` | `maxio_advanced_billing/errors/read_subscription_component_error.py` |

### client.subscription_components.record_event

- **Route**: `POST /events/{api_handle}.json`
- **Server**: `ebb`
- **Signature**: `def record_event(api_handle: str, *, store_uid: str | None = None, body: EbbEvent | EbbEventDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `api_handle`
- **Params**: `api_handle` — path · `store_uid` — query · `body` — JSON body
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `EbbEvent` | `maxio_advanced_billing/models/ebb_event.py` |
| `EbbEventDict` | `maxio_advanced_billing/models/ebb_event.py` |

### client.subscription_components.update_prepaid_usage_allocation_expiration_date

- **Route**: `PUT /subscriptions/{subscription_id}/components/{component_id}/allocations/{allocation_id}.json`
- **Server**: `production`
- **Signature**: `def update_prepaid_usage_allocation_expiration_date(subscription_id: int, component_id: int, allocation_id: int, *, body: UpdateAllocationExpirationDate | UpdateAllocationExpirationDateDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `component_id`, `allocation_id`
- **Params**: `subscription_id` — path · `component_id` — path · `allocation_id` — path · `body` — JSON body
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, UpdatePrepaidUsageAllocationExpirationDateErrorBody]`
- **Error**: `UpdatePrepaidUsageAllocationExpirationDateErrorBody` — **Case A (typed)**
- **Error arms**: `SubscriptionComponentAllocationError1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `UpdateAllocationExpirationDate` | `maxio_advanced_billing/models/update_allocation_expiration_date.py` |
| `UpdateAllocationExpirationDateDict` | `maxio_advanced_billing/models/update_allocation_expiration_date.py` |
| `UpdatePrepaidUsageAllocationExpirationDateErrorBody` | `maxio_advanced_billing/errors/update_prepaid_usage_allocation_expiration_date_error.py` |
| `SubscriptionComponentAllocationError1` | `maxio_advanced_billing/models/subscription_component_allocation_error1.py` |

