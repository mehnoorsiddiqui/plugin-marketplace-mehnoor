<!-- Generated file — do not edit; regenerated with the SDK. -->

# ApiExports — operations

Accessor: `client.api_exports` · Source: `maxio_advanced_billing/apis/api_exports.py` · 9 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.api_exports.export_invoices

- **Route**: `POST /api_exports/invoices.json`
- **Server**: `production`
- **Signature**: `def export_invoices(*, request_options: RequestOptionsOrDict | None = None)`
- **Returns (parsed)**: `BatchJobResponse`
- **Returns (raw)**: `ApiResult[BatchJobResponse, ExportInvoicesErrorBody]`
- **Error**: `ExportInvoicesErrorBody` — **Case A (typed)**
- **Error arms**: `SingleErrorResponse1` [409] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `BatchJobResponse` | `maxio_advanced_billing/models/batch_job_response.py` |
| `ExportInvoicesErrorBody` | `maxio_advanced_billing/errors/export_invoices_error.py` |
| `SingleErrorResponse1` | `maxio_advanced_billing/models/single_error_response1.py` |

### client.api_exports.export_proforma_invoices

- **Route**: `POST /api_exports/proforma_invoices.json`
- **Server**: `production`
- **Signature**: `def export_proforma_invoices(*, request_options: RequestOptionsOrDict | None = None)`
- **Returns (parsed)**: `BatchJobResponse`
- **Returns (raw)**: `ApiResult[BatchJobResponse, ExportProformaInvoicesErrorBody]`
- **Error**: `ExportProformaInvoicesErrorBody` — **Case A (typed)**
- **Error arms**: `SingleErrorResponse1` [409] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `BatchJobResponse` | `maxio_advanced_billing/models/batch_job_response.py` |
| `ExportProformaInvoicesErrorBody` | `maxio_advanced_billing/errors/export_proforma_invoices_error.py` |
| `SingleErrorResponse1` | `maxio_advanced_billing/models/single_error_response1.py` |

### client.api_exports.export_subscriptions

- **Route**: `POST /api_exports/subscriptions.json`
- **Server**: `production`
- **Signature**: `def export_subscriptions(*, request_options: RequestOptionsOrDict | None = None)`
- **Returns (parsed)**: `BatchJobResponse`
- **Returns (raw)**: `ApiResult[BatchJobResponse, ExportSubscriptionsErrorBody]`
- **Error**: `ExportSubscriptionsErrorBody` — **Case A (typed)**
- **Error arms**: `SingleErrorResponse1` [409] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `BatchJobResponse` | `maxio_advanced_billing/models/batch_job_response.py` |
| `ExportSubscriptionsErrorBody` | `maxio_advanced_billing/errors/export_subscriptions_error.py` |
| `SingleErrorResponse1` | `maxio_advanced_billing/models/single_error_response1.py` |

### client.api_exports.list_exported_invoices

- **Route**: `GET /api_exports/invoices/{batch_id}/rows.json`
- **Server**: `production`
- **Signature**: `def list_exported_invoices(batch_id: str, *, per_page: int | None = 100, page: int | None = 1, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `batch_id`
- **Params**: `batch_id` — path · `per_page` — query · `page` — query
- **Returns (parsed)**: `list[Invoice]`
- **Returns (raw)**: `ApiResult[list[Invoice], ListExportedInvoicesErrorBody]`
- **Error**: `ListExportedInvoicesErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `Invoice` | `maxio_advanced_billing/models/invoice.py` |
| `ListExportedInvoicesErrorBody` | `maxio_advanced_billing/errors/list_exported_invoices_error.py` |

### client.api_exports.list_exported_proforma_invoices

- **Route**: `GET /api_exports/proforma_invoices/{batch_id}/rows.json`
- **Server**: `production`
- **Signature**: `def list_exported_proforma_invoices(batch_id: str, *, per_page: int | None = 100, page: int | None = 1, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `batch_id`
- **Params**: `batch_id` — path · `per_page` — query · `page` — query
- **Returns (parsed)**: `list[ProformaInvoice]`
- **Returns (raw)**: `ApiResult[list[ProformaInvoice], ListExportedProformaInvoicesErrorBody]`
- **Error**: `ListExportedProformaInvoicesErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `ProformaInvoice` | `maxio_advanced_billing/models/proforma_invoice.py` |
| `ListExportedProformaInvoicesErrorBody` | `maxio_advanced_billing/errors/list_exported_proforma_invoices_error.py` |

### client.api_exports.list_exported_subscriptions

- **Route**: `GET /api_exports/subscriptions/{batch_id}/rows.json`
- **Server**: `production`
- **Signature**: `def list_exported_subscriptions(batch_id: str, *, per_page: int | None = 100, page: int | None = 1, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `batch_id`
- **Params**: `batch_id` — path · `per_page` — query · `page` — query
- **Returns (parsed)**: `list[Subscription]`
- **Returns (raw)**: `ApiResult[list[Subscription], ListExportedSubscriptionsErrorBody]`
- **Error**: `ListExportedSubscriptionsErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `Subscription` | `maxio_advanced_billing/models/subscription.py` |
| `ListExportedSubscriptionsErrorBody` | `maxio_advanced_billing/errors/list_exported_subscriptions_error.py` |

### client.api_exports.read_invoices_export

- **Route**: `GET /api_exports/invoices/{batch_id}.json`
- **Server**: `production`
- **Signature**: `def read_invoices_export(batch_id: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `batch_id`
- **Params**: `batch_id` — path
- **Returns (parsed)**: `BatchJobResponse`
- **Returns (raw)**: `ApiResult[BatchJobResponse, ReadInvoicesExportErrorBody]`
- **Error**: `ReadInvoicesExportErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `BatchJobResponse` | `maxio_advanced_billing/models/batch_job_response.py` |
| `ReadInvoicesExportErrorBody` | `maxio_advanced_billing/errors/read_invoices_export_error.py` |

### client.api_exports.read_proforma_invoices_export

- **Route**: `GET /api_exports/proforma_invoices/{batch_id}.json`
- **Server**: `production`
- **Signature**: `def read_proforma_invoices_export(batch_id: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `batch_id`
- **Params**: `batch_id` — path
- **Returns (parsed)**: `BatchJobResponse`
- **Returns (raw)**: `ApiResult[BatchJobResponse, ReadProformaInvoicesExportErrorBody]`
- **Error**: `ReadProformaInvoicesExportErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `BatchJobResponse` | `maxio_advanced_billing/models/batch_job_response.py` |
| `ReadProformaInvoicesExportErrorBody` | `maxio_advanced_billing/errors/read_proforma_invoices_export_error.py` |

### client.api_exports.read_subscriptions_export

- **Route**: `GET /api_exports/subscriptions/{batch_id}.json`
- **Server**: `production`
- **Signature**: `def read_subscriptions_export(batch_id: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `batch_id`
- **Params**: `batch_id` — path
- **Returns (parsed)**: `BatchJobResponse`
- **Returns (raw)**: `ApiResult[BatchJobResponse, ReadSubscriptionsExportErrorBody]`
- **Error**: `ReadSubscriptionsExportErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `BatchJobResponse` | `maxio_advanced_billing/models/batch_job_response.py` |
| `ReadSubscriptionsExportErrorBody` | `maxio_advanced_billing/errors/read_subscriptions_export_error.py` |

