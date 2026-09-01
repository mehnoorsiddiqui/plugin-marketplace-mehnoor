<!-- Generated file — do not edit; regenerated with the SDK. -->

# SubscriptionStatus — operations

Accessor: `client.subscription_status` · Source: `maxio_advanced_billing/apis/subscription_status.py` · 10 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.subscription_status.cancel_delayed_cancellation

- **Route**: `DELETE /subscriptions/{subscription_id}/delayed_cancel.json`
- **Server**: `production`
- **Signature**: `def cancel_delayed_cancellation(subscription_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path
- **Returns (parsed)**: `DelayedCancellationResponse`
- **Returns (raw)**: `ApiResult[DelayedCancellationResponse, CancelDelayedCancellationErrorBody]`
- **Error**: `CancelDelayedCancellationErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `DelayedCancellationResponse` | `maxio_advanced_billing/models/delayed_cancellation_response.py` |
| `CancelDelayedCancellationErrorBody` | `maxio_advanced_billing/errors/cancel_delayed_cancellation_error.py` |

### client.subscription_status.cancel_dunning

- **Route**: `POST /subscriptions/{subscription_id}/cancel_dunning.json`
- **Server**: `production`
- **Signature**: `def cancel_dunning(subscription_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, CancelDunningErrorBody]`
- **Error**: `CancelDunningErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `CancelDunningErrorBody` | `maxio_advanced_billing/errors/cancel_dunning_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_status.cancel_subscription

- **Route**: `DELETE /subscriptions/{subscription_id}.json`
- **Server**: `production`
- **Signature**: `def cancel_subscription(subscription_id: int, *, body: CancellationRequest | CancellationRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, CancelSubscriptionErrorBody]`
- **Error**: `CancelSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `CancelSubscriptionErrorResponse` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `CancellationRequest` | `maxio_advanced_billing/models/cancellation_request.py` |
| `CancellationRequestDict` | `maxio_advanced_billing/models/cancellation_request.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `CancelSubscriptionErrorBody` | `maxio_advanced_billing/errors/cancel_subscription_error.py` |
| `CancelSubscriptionErrorResponse` | `maxio_advanced_billing/models/unions/cancel_subscription_error_response.py` |

### client.subscription_status.initiate_delayed_cancellation

- **Route**: `POST /subscriptions/{subscription_id}/delayed_cancel.json`
- **Server**: `production`
- **Signature**: `def initiate_delayed_cancellation(subscription_id: int, *, body: CancellationRequest | CancellationRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `DelayedCancellationResponse`
- **Returns (raw)**: `ApiResult[DelayedCancellationResponse, InitiateDelayedCancellationErrorBody]`
- **Error**: `InitiateDelayedCancellationErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `CancellationRequest` | `maxio_advanced_billing/models/cancellation_request.py` |
| `CancellationRequestDict` | `maxio_advanced_billing/models/cancellation_request.py` |
| `DelayedCancellationResponse` | `maxio_advanced_billing/models/delayed_cancellation_response.py` |
| `InitiateDelayedCancellationErrorBody` | `maxio_advanced_billing/errors/initiate_delayed_cancellation_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_status.pause_subscription

- **Route**: `POST /subscriptions/{subscription_id}/hold.json`
- **Server**: `production`
- **Signature**: `def pause_subscription(subscription_id: int, *, body: PauseRequest | PauseRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, PauseSubscriptionErrorBody]`
- **Error**: `PauseSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `PauseRequest` | `maxio_advanced_billing/models/pause_request.py` |
| `PauseRequestDict` | `maxio_advanced_billing/models/pause_request.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `PauseSubscriptionErrorBody` | `maxio_advanced_billing/errors/pause_subscription_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_status.preview_renewal

- **Route**: `POST /subscriptions/{subscription_id}/renewals/preview.json`
- **Server**: `production`
- **Signature**: `def preview_renewal(subscription_id: int, *, body: RenewalPreviewRequest | RenewalPreviewRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `RenewalPreviewResponse`
- **Returns (raw)**: `ApiResult[RenewalPreviewResponse, PreviewRenewalErrorBody]`
- **Error**: `PreviewRenewalErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `RenewalPreviewRequest` | `maxio_advanced_billing/models/renewal_preview_request.py` |
| `RenewalPreviewRequestDict` | `maxio_advanced_billing/models/renewal_preview_request.py` |
| `RenewalPreviewResponse` | `maxio_advanced_billing/models/renewal_preview_response.py` |
| `PreviewRenewalErrorBody` | `maxio_advanced_billing/errors/preview_renewal_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_status.reactivate_subscription

- **Route**: `PUT /subscriptions/{subscription_id}/reactivate.json`
- **Server**: `production`
- **Signature**: `def reactivate_subscription(subscription_id: int, *, body: ReactivateSubscriptionRequest | ReactivateSubscriptionRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, ReactivateSubscriptionErrorBody]`
- **Error**: `ReactivateSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ReactivateSubscriptionRequest` | `maxio_advanced_billing/models/reactivate_subscription_request.py` |
| `ReactivateSubscriptionRequestDict` | `maxio_advanced_billing/models/reactivate_subscription_request.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `ReactivateSubscriptionErrorBody` | `maxio_advanced_billing/errors/reactivate_subscription_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_status.resume_subscription

- **Route**: `POST /subscriptions/{subscription_id}/resume.json`
- **Server**: `production`
- **Signature**: `def resume_subscription(subscription_id: int, *, calendar_billing_resumption_charge: ResumptionChargeOrStr | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `calendar_billing_resumption_charge` — query `calendar_billing['resumption_charge']`
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, ResumeSubscriptionErrorBody]`
- **Error**: `ResumeSubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ResumptionChargeOrStr` | `maxio_advanced_billing/models/enums/resumption_charge.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `ResumeSubscriptionErrorBody` | `maxio_advanced_billing/errors/resume_subscription_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_status.retry_subscription

- **Route**: `PUT /subscriptions/{subscription_id}/retry.json`
- **Server**: `production`
- **Signature**: `def retry_subscription(subscription_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, RetrySubscriptionErrorBody]`
- **Error**: `RetrySubscriptionErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `RetrySubscriptionErrorBody` | `maxio_advanced_billing/errors/retry_subscription_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_status.update_automatic_subscription_resumption

- **Route**: `PUT /subscriptions/{subscription_id}/hold.json`
- **Server**: `production`
- **Signature**: `def update_automatic_subscription_resumption(subscription_id: int, *, body: PauseRequest | PauseRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, UpdateAutomaticSubscriptionResumptionErrorBody]`
- **Error**: `UpdateAutomaticSubscriptionResumptionErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `PauseRequest` | `maxio_advanced_billing/models/pause_request.py` |
| `PauseRequestDict` | `maxio_advanced_billing/models/pause_request.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `UpdateAutomaticSubscriptionResumptionErrorBody` | `maxio_advanced_billing/errors/update_automatic_subscription_resumption_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

