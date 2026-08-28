<!-- Generated file — do not edit; regenerated with the SDK. -->

# SubscriptionGroupStatus — operations

Accessor: `client.subscription_group_status` · Source: `maxio_advanced_billing/apis/subscription_group_status.py` · 4 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.subscription_group_status.cancel_delayed_cancellation_for_group

- **Route**: `DELETE /subscription_groups/{uid}/delayed_cancel.json`
- **Server**: `production`
- **Signature**: `def cancel_delayed_cancellation_for_group(uid: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, CancelDelayedCancellationForGroupErrorBody]`
- **Error**: `CancelDelayedCancellationForGroupErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CancelDelayedCancellationForGroupErrorBody` | `maxio_advanced_billing/errors/cancel_delayed_cancellation_for_group_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_group_status.cancel_subscriptions_in_group

- **Route**: `POST /subscription_groups/{uid}/cancel.json`
- **Server**: `production`
- **Signature**: `def cancel_subscriptions_in_group(uid: str, *, body: CancelGroupedSubscriptionsRequest | CancelGroupedSubscriptionsRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path · `body` — JSON body
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, CancelSubscriptionsInGroupErrorBody]`
- **Error**: `CancelSubscriptionsInGroupErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CancelGroupedSubscriptionsRequest` | `maxio_advanced_billing/models/cancel_grouped_subscriptions_request.py` |
| `CancelGroupedSubscriptionsRequestDict` | `maxio_advanced_billing/models/cancel_grouped_subscriptions_request.py` |
| `CancelSubscriptionsInGroupErrorBody` | `maxio_advanced_billing/errors/cancel_subscriptions_in_group_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_group_status.initiate_delayed_cancellation_for_group

- **Route**: `POST /subscription_groups/{uid}/delayed_cancel.json`
- **Server**: `production`
- **Signature**: `def initiate_delayed_cancellation_for_group(uid: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, InitiateDelayedCancellationForGroupErrorBody]`
- **Error**: `InitiateDelayedCancellationForGroupErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `InitiateDelayedCancellationForGroupErrorBody` | `maxio_advanced_billing/errors/initiate_delayed_cancellation_for_group_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_group_status.reactivate_subscription_group

- **Route**: `POST /subscription_groups/{uid}/reactivate.json`
- **Server**: `production`
- **Signature**: `def reactivate_subscription_group(uid: str, *, body: ReactivateSubscriptionGroupRequest | ReactivateSubscriptionGroupRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path · `body` — JSON body
- **Returns (parsed)**: `ReactivateSubscriptionGroupResponse`
- **Returns (raw)**: `ApiResult[ReactivateSubscriptionGroupResponse, ReactivateSubscriptionGroupErrorBody]`
- **Error**: `ReactivateSubscriptionGroupErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ReactivateSubscriptionGroupRequest` | `maxio_advanced_billing/models/reactivate_subscription_group_request.py` |
| `ReactivateSubscriptionGroupRequestDict` | `maxio_advanced_billing/models/reactivate_subscription_group_request.py` |
| `ReactivateSubscriptionGroupResponse` | `maxio_advanced_billing/models/reactivate_subscription_group_response.py` |
| `ReactivateSubscriptionGroupErrorBody` | `maxio_advanced_billing/errors/reactivate_subscription_group_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

