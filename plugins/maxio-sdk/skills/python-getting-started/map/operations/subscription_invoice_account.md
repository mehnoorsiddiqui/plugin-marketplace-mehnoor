<!-- Generated file — do not edit; regenerated with the SDK. -->

# SubscriptionInvoiceAccount — operations

Accessor: `client.subscription_invoice_account` · Source: `maxio_advanced_billing/apis/subscription_invoice_account.py` · 7 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.subscription_invoice_account.create_prepayment

- **Route**: `POST /subscriptions/{subscription_id}/prepayments.json`
- **Server**: `production`
- **Signature**: `def create_prepayment(subscription_id: int, *, body: CreatePrepaymentRequest | CreatePrepaymentRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `CreatePrepaymentResponse`
- **Returns (raw)**: `ApiResult[CreatePrepaymentResponse, CreatePrepaymentErrorBody]`
- **Error**: `CreatePrepaymentErrorBody` — **Case A (typed)**
- **Error arms**: `CreatePrepaymentErrorResponse` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreatePrepaymentRequest` | `maxio_advanced_billing/models/create_prepayment_request.py` |
| `CreatePrepaymentRequestDict` | `maxio_advanced_billing/models/create_prepayment_request.py` |
| `CreatePrepaymentResponse` | `maxio_advanced_billing/models/create_prepayment_response.py` |
| `CreatePrepaymentErrorBody` | `maxio_advanced_billing/errors/create_prepayment_error.py` |
| `CreatePrepaymentErrorResponse` | `maxio_advanced_billing/models/unions/create_prepayment_error_response.py` |

### client.subscription_invoice_account.deduct_service_credit

- **Route**: `POST /subscriptions/{subscription_id}/service_credit_deductions.json`
- **Server**: `production`
- **Signature**: `def deduct_service_credit(subscription_id: int, *, body: DeductServiceCreditRequest | DeductServiceCreditRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, DeductServiceCreditErrorBody]`
- **Error**: `DeductServiceCreditErrorBody` — **Case A (typed)**
- **Error arms**: `DeductServiceCreditErrorResponse` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `DeductServiceCreditRequest` | `maxio_advanced_billing/models/deduct_service_credit_request.py` |
| `DeductServiceCreditRequestDict` | `maxio_advanced_billing/models/deduct_service_credit_request.py` |
| `DeductServiceCreditErrorBody` | `maxio_advanced_billing/errors/deduct_service_credit_error.py` |
| `DeductServiceCreditErrorResponse` | `maxio_advanced_billing/models/unions/deduct_service_credit_error_response.py` |

### client.subscription_invoice_account.issue_service_credit

- **Route**: `POST /subscriptions/{subscription_id}/service_credits.json`
- **Server**: `production`
- **Signature**: `def issue_service_credit(subscription_id: int, *, body: IssueServiceCreditRequest | IssueServiceCreditRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `ServiceCredit`
- **Returns (raw)**: `ApiResult[ServiceCredit, IssueServiceCreditErrorBody]`
- **Error**: `IssueServiceCreditErrorBody` — **Case A (typed)**
- **Error arms**: `IssueServiceCreditErrorResponse` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `IssueServiceCreditRequest` | `maxio_advanced_billing/models/issue_service_credit_request.py` |
| `IssueServiceCreditRequestDict` | `maxio_advanced_billing/models/issue_service_credit_request.py` |
| `ServiceCredit` | `maxio_advanced_billing/models/service_credit.py` |
| `IssueServiceCreditErrorBody` | `maxio_advanced_billing/errors/issue_service_credit_error.py` |
| `IssueServiceCreditErrorResponse` | `maxio_advanced_billing/models/unions/issue_service_credit_error_response.py` |

### client.subscription_invoice_account.list_prepayments

- **Route**: `GET /subscriptions/{subscription_id}/prepayments.json`
- **Server**: `production`
- **Signature**: `def list_prepayments(subscription_id: int, *, page: int | None = 1, per_page: int | None = 20, filter: ListPrepaymentsFilter | ListPrepaymentsFilterDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `page` — query · `per_page` — query · `filter` — query
- **Returns (parsed)**: `PrepaymentsResponse`
- **Returns (raw)**: `ApiResult[PrepaymentsResponse, ListPrepaymentsErrorBody]`
- **Error**: `ListPrepaymentsErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `ListPrepaymentsFilter` | `maxio_advanced_billing/models/list_prepayments_filter.py` |
| `ListPrepaymentsFilterDict` | `maxio_advanced_billing/models/list_prepayments_filter.py` |
| `PrepaymentsResponse` | `maxio_advanced_billing/models/prepayments_response.py` |
| `ListPrepaymentsErrorBody` | `maxio_advanced_billing/errors/list_prepayments_error.py` |

### client.subscription_invoice_account.list_service_credits

- **Route**: `GET /subscriptions/{subscription_id}/service_credits/list.json`
- **Server**: `production`
- **Signature**: `def list_service_credits(subscription_id: int, *, page: int | None = 1, per_page: int | None = 20, direction: SortingDirectionOrStr | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `page` — query · `per_page` — query · `direction` — query
- **Returns (parsed)**: `ListServiceCreditsResponse`
- **Returns (raw)**: `ApiResult[ListServiceCreditsResponse, ListServiceCreditsErrorBody]`
- **Error**: `ListServiceCreditsErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `SortingDirectionOrStr` | `maxio_advanced_billing/models/enums/sorting_direction.py` |
| `ListServiceCreditsResponse` | `maxio_advanced_billing/models/list_service_credits_response.py` |
| `ListServiceCreditsErrorBody` | `maxio_advanced_billing/errors/list_service_credits_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_invoice_account.read_account_balances

- **Route**: `GET /subscriptions/{subscription_id}/account_balances.json`
- **Server**: `production`
- **Signature**: `def read_account_balances(subscription_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path
- **Returns (parsed)**: `AccountBalances`
- **Returns (raw)**: `ApiResult[AccountBalances, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `AccountBalances` | `maxio_advanced_billing/models/account_balances.py` |

### client.subscription_invoice_account.refund_prepayment

- **Route**: `POST /subscriptions/{subscription_id}/prepayments/{prepayment_id}/refunds.json`
- **Server**: `production`
- **Signature**: `def refund_prepayment(subscription_id: int, prepayment_id: int, *, body: RefundPrepaymentRequest | RefundPrepaymentRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`, `prepayment_id`
- **Params**: `subscription_id` — path · `prepayment_id` — path · `body` — JSON body
- **Returns (parsed)**: `PrepaymentResponse`
- **Returns (raw)**: `ApiResult[PrepaymentResponse, RefundPrepaymentErrorBody]`
- **Error**: `RefundPrepaymentErrorBody` — **Case A (typed)**
- **Error arms**: `RefundPrepaymentBaseErrorsResponse1` [400] · `str` [404] · `RefundPrepaymentErrorResponse` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `RefundPrepaymentRequest` | `maxio_advanced_billing/models/refund_prepayment_request.py` |
| `RefundPrepaymentRequestDict` | `maxio_advanced_billing/models/refund_prepayment_request.py` |
| `PrepaymentResponse` | `maxio_advanced_billing/models/prepayment_response.py` |
| `RefundPrepaymentErrorBody` | `maxio_advanced_billing/errors/refund_prepayment_error.py` |
| `RefundPrepaymentBaseErrorsResponse1` | `maxio_advanced_billing/models/refund_prepayment_base_errors_response1.py` |
| `RefundPrepaymentErrorResponse` | `maxio_advanced_billing/models/unions/refund_prepayment_error_response.py` |

