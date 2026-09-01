<!-- Generated file — do not edit; regenerated with the SDK. -->

# AdvanceInvoice — operations

Accessor: `client.advance_invoice` · Source: `maxio_advanced_billing/apis/advance_invoice.py` · 3 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.advance_invoice.issue_advance_invoice

- **Route**: `POST /subscriptions/{subscription_id}/advance_invoice/issue.json`
- **Server**: `production`
- **Signature**: `def issue_advance_invoice(subscription_id: int, *, body: IssueAdvanceInvoiceRequest | IssueAdvanceInvoiceRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `Invoice`
- **Returns (raw)**: `ApiResult[Invoice, IssueAdvanceInvoiceErrorBody]`
- **Error**: `IssueAdvanceInvoiceErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `IssueAdvanceInvoiceRequest` | `maxio_advanced_billing/models/issue_advance_invoice_request.py` |
| `IssueAdvanceInvoiceRequestDict` | `maxio_advanced_billing/models/issue_advance_invoice_request.py` |
| `Invoice` | `maxio_advanced_billing/models/invoice.py` |
| `IssueAdvanceInvoiceErrorBody` | `maxio_advanced_billing/errors/issue_advance_invoice_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.advance_invoice.read_advance_invoice

- **Route**: `GET /subscriptions/{subscription_id}/advance_invoice.json`
- **Server**: `production`
- **Signature**: `def read_advance_invoice(subscription_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path
- **Returns (parsed)**: `Invoice`
- **Returns (raw)**: `ApiResult[Invoice, ReadAdvanceInvoiceErrorBody]`
- **Error**: `ReadAdvanceInvoiceErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `Invoice` | `maxio_advanced_billing/models/invoice.py` |
| `ReadAdvanceInvoiceErrorBody` | `maxio_advanced_billing/errors/read_advance_invoice_error.py` |

### client.advance_invoice.void_advance_invoice

- **Route**: `POST /subscriptions/{subscription_id}/advance_invoice/void.json`
- **Server**: `production`
- **Signature**: `def void_advance_invoice(subscription_id: int, *, body: VoidInvoiceRequest | VoidInvoiceRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `Invoice`
- **Returns (raw)**: `ApiResult[Invoice, VoidAdvanceInvoiceErrorBody]`
- **Error**: `VoidAdvanceInvoiceErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `VoidInvoiceRequest` | `maxio_advanced_billing/models/void_invoice_request.py` |
| `VoidInvoiceRequestDict` | `maxio_advanced_billing/models/void_invoice_request.py` |
| `Invoice` | `maxio_advanced_billing/models/invoice.py` |
| `VoidAdvanceInvoiceErrorBody` | `maxio_advanced_billing/errors/void_advance_invoice_error.py` |

