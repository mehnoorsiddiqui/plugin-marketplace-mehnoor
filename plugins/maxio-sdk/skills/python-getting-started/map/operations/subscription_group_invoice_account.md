<!-- Generated file — do not edit; regenerated with the SDK. -->

# SubscriptionGroupInvoiceAccount — operations

Accessor: `client.subscription_group_invoice_account` · Source: `maxio_advanced_billing/apis/subscription_group_invoice_account.py` · 4 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.subscription_group_invoice_account.create_subscription_group_prepayment

- **Route**: `POST /subscription_groups/{uid}/prepayments.json`
- **Server**: `production`
- **Signature**: `def create_subscription_group_prepayment(uid: str, *, body: SubscriptionGroupPrepaymentRequest | SubscriptionGroupPrepaymentRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionGroupPrepaymentResponse`
- **Returns (raw)**: `ApiResult[SubscriptionGroupPrepaymentResponse, CreateSubscriptionGroupPrepaymentErrorBody]`
- **Error**: `CreateSubscriptionGroupPrepaymentErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `SubscriptionGroupPrepaymentRequest` | `maxio_advanced_billing/models/subscription_group_prepayment_request.py` |
| `SubscriptionGroupPrepaymentRequestDict` | `maxio_advanced_billing/models/subscription_group_prepayment_request.py` |
| `SubscriptionGroupPrepaymentResponse` | `maxio_advanced_billing/models/subscription_group_prepayment_response.py` |
| `CreateSubscriptionGroupPrepaymentErrorBody` | `maxio_advanced_billing/errors/create_subscription_group_prepayment_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_group_invoice_account.deduct_subscription_group_service_credit

- **Route**: `POST /subscription_groups/{uid}/service_credit_deductions.json`
- **Server**: `production`
- **Signature**: `def deduct_subscription_group_service_credit(uid: str, *, body: DeductServiceCreditRequest | DeductServiceCreditRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path · `body` — JSON body
- **Returns (parsed)**: `ServiceCredit`
- **Returns (raw)**: `ApiResult[ServiceCredit, DeductSubscriptionGroupServiceCreditErrorBody]`
- **Error**: `DeductSubscriptionGroupServiceCreditErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `DeductServiceCreditRequest` | `maxio_advanced_billing/models/deduct_service_credit_request.py` |
| `DeductServiceCreditRequestDict` | `maxio_advanced_billing/models/deduct_service_credit_request.py` |
| `ServiceCredit` | `maxio_advanced_billing/models/service_credit.py` |
| `DeductSubscriptionGroupServiceCreditErrorBody` | `maxio_advanced_billing/errors/deduct_subscription_group_service_credit_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_group_invoice_account.issue_subscription_group_service_credit

- **Route**: `POST /subscription_groups/{uid}/service_credits.json`
- **Server**: `production`
- **Signature**: `def issue_subscription_group_service_credit(uid: str, *, body: IssueServiceCreditRequest | IssueServiceCreditRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path · `body` — JSON body
- **Returns (parsed)**: `ServiceCreditResponse`
- **Returns (raw)**: `ApiResult[ServiceCreditResponse, IssueSubscriptionGroupServiceCreditErrorBody]`
- **Error**: `IssueSubscriptionGroupServiceCreditErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `IssueServiceCreditRequest` | `maxio_advanced_billing/models/issue_service_credit_request.py` |
| `IssueServiceCreditRequestDict` | `maxio_advanced_billing/models/issue_service_credit_request.py` |
| `ServiceCreditResponse` | `maxio_advanced_billing/models/service_credit_response.py` |
| `IssueSubscriptionGroupServiceCreditErrorBody` | `maxio_advanced_billing/errors/issue_subscription_group_service_credit_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_group_invoice_account.list_prepayments_for_subscription_group

- **Route**: `GET /subscription_groups/{uid}/prepayments.json`
- **Server**: `production`
- **Signature**: `def list_prepayments_for_subscription_group(uid: str, *, page: int | None = 1, per_page: int | None = 20, filter: ListPrepaymentsFilter | ListPrepaymentsFilterDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path · `page` — query · `per_page` — query · `filter` — query
- **Returns (parsed)**: `ListSubscriptionGroupPrepaymentResponse`
- **Returns (raw)**: `ApiResult[ListSubscriptionGroupPrepaymentResponse, ListPrepaymentsForSubscriptionGroupErrorBody]`
- **Error**: `ListPrepaymentsForSubscriptionGroupErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `ListPrepaymentsFilter` | `maxio_advanced_billing/models/list_prepayments_filter.py` |
| `ListPrepaymentsFilterDict` | `maxio_advanced_billing/models/list_prepayments_filter.py` |
| `ListSubscriptionGroupPrepaymentResponse` | `maxio_advanced_billing/models/list_subscription_group_prepayment_response.py` |
| `ListPrepaymentsForSubscriptionGroupErrorBody` | `maxio_advanced_billing/errors/list_prepayments_for_subscription_group_error.py` |

