<!-- Generated file — do not edit; regenerated with the SDK. -->

# ProformaInvoices — operations

Accessor: `client.proforma_invoices` · Source: `maxio_advanced_billing/apis/proforma_invoices.py` · 10 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.proforma_invoices.create_consolidated_proforma_invoice

- **Route**: `POST /subscription_groups/{uid}/proforma_invoices.json`
- **Server**: `production`
- **Signature**: `def create_consolidated_proforma_invoice(uid: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, CreateConsolidatedProformaInvoiceErrorBody]`
- **Error**: `CreateConsolidatedProformaInvoiceErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateConsolidatedProformaInvoiceErrorBody` | `maxio_advanced_billing/errors/create_consolidated_proforma_invoice_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.proforma_invoices.create_proforma_invoice

- **Route**: `POST /subscriptions/{subscription_id}/proforma_invoices.json`
- **Server**: `production`
- **Signature**: `def create_proforma_invoice(subscription_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path
- **Returns (parsed)**: `ProformaInvoice`
- **Returns (raw)**: `ApiResult[ProformaInvoice, CreateProformaInvoiceErrorBody]`
- **Error**: `CreateProformaInvoiceErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ProformaInvoice` | `maxio_advanced_billing/models/proforma_invoice.py` |
| `CreateProformaInvoiceErrorBody` | `maxio_advanced_billing/errors/create_proforma_invoice_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.proforma_invoices.create_signup_proforma_invoice

- **Route**: `POST /subscriptions/proforma_invoices.json`
- **Server**: `production`
- **Signature**: `def create_signup_proforma_invoice(*, body: CreateSubscriptionRequest | CreateSubscriptionRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `body` — JSON body
- **Returns (parsed)**: `ProformaInvoice`
- **Returns (raw)**: `ApiResult[ProformaInvoice, CreateSignupProformaInvoiceErrorBody]`
- **Error**: `CreateSignupProformaInvoiceErrorBody` — **Case A (typed)**
- **Error arms**: `ProformaBadRequestErrorResponse1` [400] · `ErrorArrayMapResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateSubscriptionRequest` | `maxio_advanced_billing/models/create_subscription_request.py` |
| `CreateSubscriptionRequestDict` | `maxio_advanced_billing/models/create_subscription_request.py` |
| `ProformaInvoice` | `maxio_advanced_billing/models/proforma_invoice.py` |
| `CreateSignupProformaInvoiceErrorBody` | `maxio_advanced_billing/errors/create_signup_proforma_invoice_error.py` |
| `ProformaBadRequestErrorResponse1` | `maxio_advanced_billing/models/proforma_bad_request_error_response1.py` |
| `ErrorArrayMapResponse1` | `maxio_advanced_billing/models/error_array_map_response1.py` |

### client.proforma_invoices.deliver_proforma_invoice

- **Route**: `POST /proforma_invoices/{proforma_invoice_uid}/deliveries.json`
- **Server**: `production`
- **Signature**: `def deliver_proforma_invoice(proforma_invoice_uid: str, *, body: DeliverProformaInvoiceRequest | DeliverProformaInvoiceRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `proforma_invoice_uid`
- **Params**: `proforma_invoice_uid` — path · `body` — JSON body
- **Returns (parsed)**: `ProformaInvoice`
- **Returns (raw)**: `ApiResult[ProformaInvoice, DeliverProformaInvoiceErrorBody]`
- **Error**: `DeliverProformaInvoiceErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `DeliverProformaInvoiceRequest` | `maxio_advanced_billing/models/deliver_proforma_invoice_request.py` |
| `DeliverProformaInvoiceRequestDict` | `maxio_advanced_billing/models/deliver_proforma_invoice_request.py` |
| `ProformaInvoice` | `maxio_advanced_billing/models/proforma_invoice.py` |
| `DeliverProformaInvoiceErrorBody` | `maxio_advanced_billing/errors/deliver_proforma_invoice_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.proforma_invoices.list_proforma_invoices

- **Route**: `GET /subscriptions/{subscription_id}/proforma_invoices.json`
- **Server**: `production`
- **Signature**: `def list_proforma_invoices(subscription_id: int, *, start_date: str | None = None, end_date: str | None = None, status: ProformaInvoiceStatusOrStr | None = None, page: int | None = 1, per_page: int | None = 20, direction: DirectionOrStr | None = None, line_items: bool | None = False, discounts: bool | None = False, taxes: bool | None = False, credits: bool | None = False, payments: bool | None = False, custom_fields: bool | None = False, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `start_date` — query · `end_date` — query · `status` — query · `page` — query · `per_page` — query · `direction` — query · `line_items` — query · `discounts` — query · `taxes` — query · `credits` — query · `payments` — query · `custom_fields` — query
- **Returns (parsed)**: `ListProformaInvoicesResponse`
- **Returns (raw)**: `ApiResult[ListProformaInvoicesResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ProformaInvoiceStatusOrStr` | `maxio_advanced_billing/models/enums/proforma_invoice_status.py` |
| `DirectionOrStr` | `maxio_advanced_billing/models/enums/direction.py` |
| `ListProformaInvoicesResponse` | `maxio_advanced_billing/models/list_proforma_invoices_response.py` |

### client.proforma_invoices.list_subscription_group_proforma_invoices

- **Route**: `GET /subscription_groups/{uid}/proforma_invoices.json`
- **Server**: `production`
- **Signature**: `def list_subscription_group_proforma_invoices(uid: str, *, line_items: bool | None = False, discounts: bool | None = False, taxes: bool | None = False, credits: bool | None = False, payments: bool | None = False, custom_fields: bool | None = False, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path · `line_items` — query · `discounts` — query · `taxes` — query · `credits` — query · `payments` — query · `custom_fields` — query
- **Returns (parsed)**: `ListProformaInvoicesResponse`
- **Returns (raw)**: `ApiResult[ListProformaInvoicesResponse, ListSubscriptionGroupProformaInvoicesErrorBody]`
- **Error**: `ListSubscriptionGroupProformaInvoicesErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `ListProformaInvoicesResponse` | `maxio_advanced_billing/models/list_proforma_invoices_response.py` |
| `ListSubscriptionGroupProformaInvoicesErrorBody` | `maxio_advanced_billing/errors/list_subscription_group_proforma_invoices_error.py` |

### client.proforma_invoices.preview_proforma_invoice

- **Route**: `POST /subscriptions/{subscription_id}/proforma_invoices/preview.json`
- **Server**: `production`
- **Signature**: `def preview_proforma_invoice(subscription_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path
- **Returns (parsed)**: `ProformaInvoice`
- **Returns (raw)**: `ApiResult[ProformaInvoice, PreviewProformaInvoiceErrorBody]`
- **Error**: `PreviewProformaInvoiceErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `ProformaInvoice` | `maxio_advanced_billing/models/proforma_invoice.py` |
| `PreviewProformaInvoiceErrorBody` | `maxio_advanced_billing/errors/preview_proforma_invoice_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.proforma_invoices.preview_signup_proforma_invoice

- **Route**: `POST /subscriptions/proforma_invoices/preview.json`
- **Server**: `production`
- **Signature**: `def preview_signup_proforma_invoice(*, include: CreateSignupProformaPreviewIncludeOrStr | None = None, body: CreateSubscriptionRequest | CreateSubscriptionRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `include` — query · `body` — JSON body
- **Returns (parsed)**: `SignupProformaPreviewResponse`
- **Returns (raw)**: `ApiResult[SignupProformaPreviewResponse, PreviewSignupProformaInvoiceErrorBody]`
- **Error**: `PreviewSignupProformaInvoiceErrorBody` — **Case A (typed)**
- **Error arms**: `ProformaBadRequestErrorResponse1` [400] · `ErrorArrayMapResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateSignupProformaPreviewIncludeOrStr` | `maxio_advanced_billing/models/enums/create_signup_proforma_preview_include.py` |
| `CreateSubscriptionRequest` | `maxio_advanced_billing/models/create_subscription_request.py` |
| `CreateSubscriptionRequestDict` | `maxio_advanced_billing/models/create_subscription_request.py` |
| `SignupProformaPreviewResponse` | `maxio_advanced_billing/models/signup_proforma_preview_response.py` |
| `PreviewSignupProformaInvoiceErrorBody` | `maxio_advanced_billing/errors/preview_signup_proforma_invoice_error.py` |
| `ProformaBadRequestErrorResponse1` | `maxio_advanced_billing/models/proforma_bad_request_error_response1.py` |
| `ErrorArrayMapResponse1` | `maxio_advanced_billing/models/error_array_map_response1.py` |

### client.proforma_invoices.read_proforma_invoice

- **Route**: `GET /proforma_invoices/{proforma_invoice_uid}.json`
- **Server**: `production`
- **Signature**: `def read_proforma_invoice(proforma_invoice_uid: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `proforma_invoice_uid`
- **Params**: `proforma_invoice_uid` — path
- **Returns (parsed)**: `ProformaInvoice`
- **Returns (raw)**: `ApiResult[ProformaInvoice, ReadProformaInvoiceErrorBody]`
- **Error**: `ReadProformaInvoiceErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `ProformaInvoice` | `maxio_advanced_billing/models/proforma_invoice.py` |
| `ReadProformaInvoiceErrorBody` | `maxio_advanced_billing/errors/read_proforma_invoice_error.py` |

### client.proforma_invoices.void_proforma_invoice

- **Route**: `POST /proforma_invoices/{proforma_invoice_uid}/void.json`
- **Server**: `production`
- **Signature**: `def void_proforma_invoice(proforma_invoice_uid: str, *, body: VoidInvoiceRequest | VoidInvoiceRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `proforma_invoice_uid`
- **Params**: `proforma_invoice_uid` — path · `body` — JSON body
- **Returns (parsed)**: `ProformaInvoice`
- **Returns (raw)**: `ApiResult[ProformaInvoice, VoidProformaInvoiceErrorBody]`
- **Error**: `VoidProformaInvoiceErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `VoidInvoiceRequest` | `maxio_advanced_billing/models/void_invoice_request.py` |
| `VoidInvoiceRequestDict` | `maxio_advanced_billing/models/void_invoice_request.py` |
| `ProformaInvoice` | `maxio_advanced_billing/models/proforma_invoice.py` |
| `VoidProformaInvoiceErrorBody` | `maxio_advanced_billing/errors/void_proforma_invoice_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

