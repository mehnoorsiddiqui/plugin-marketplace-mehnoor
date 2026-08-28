<!-- Generated file — do not edit; regenerated with the SDK. -->

# Customers — operations

Accessor: `client.customers` · Source: `maxio_advanced_billing/apis/customers.py` · 7 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded, and an operation with no table mentions nothing but builtins and those.

### client.customers.create_customer

- **Route**: `POST /customers.json`
- **Server**: `production`
- **Signature**: `def create_customer(*, body: CreateCustomerRequest | CreateCustomerRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `body` — JSON body
- **Returns (parsed)**: `CustomerResponse`
- **Returns (raw)**: `ApiResult[CustomerResponse, CreateCustomerErrorBody]`
- **Error**: `CreateCustomerErrorBody` — **Case A (typed)**
- **Error arms**: `CustomerErrorResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateCustomerRequest` | `maxio_advanced_billing/models/create_customer_request.py` |
| `CreateCustomerRequestDict` | `maxio_advanced_billing/models/create_customer_request.py` |
| `CustomerResponse` | `maxio_advanced_billing/models/customer_response.py` |
| `CreateCustomerErrorBody` | `maxio_advanced_billing/errors/create_customer_error.py` |
| `CustomerErrorResponse1` | `maxio_advanced_billing/models/customer_error_response1.py` |

### client.customers.delete_customer

- **Route**: `DELETE /customers/{id}.json`
- **Server**: `production`
- **Signature**: `def delete_customer(id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `id`
- **Params**: `id` — path
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, RawError]`
- **Error**: `RawError` — **Case B**

### client.customers.list_customer_subscriptions

- **Route**: `GET /customers/{customer_id}/subscriptions.json`
- **Server**: `production`
- **Signature**: `def list_customer_subscriptions(customer_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `customer_id`
- **Params**: `customer_id` — path
- **Returns (parsed)**: `list[SubscriptionResponse]`
- **Returns (raw)**: `ApiResult[list[SubscriptionResponse], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |

### client.customers.list_customers

- **Route**: `GET /customers.json`
- **Server**: `production`
- **Signature**: `def list_customers(*, direction: SortingDirectionOrStr | None = None, page: int | None = 1, per_page: int | None = 50, date_field: BasicDateFieldOrStr | None = None, start_date: str | None = None, end_date: str | None = None, start_datetime: str | None = None, end_datetime: str | None = None, q: str | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `direction` — query · `page` — query · `per_page` — query · `date_field` — query · `start_date` — query · `end_date` — query · `start_datetime` — query · `end_datetime` — query · `q` — query
- **Returns (parsed)**: `list[CustomerResponse]`
- **Returns (raw)**: `ApiResult[list[CustomerResponse], RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SortingDirectionOrStr` | `maxio_advanced_billing/models/enums/sorting_direction.py` |
| `BasicDateFieldOrStr` | `maxio_advanced_billing/models/enums/basic_date_field.py` |
| `CustomerResponse` | `maxio_advanced_billing/models/customer_response.py` |

### client.customers.read_customer

- **Route**: `GET /customers/{id}.json`
- **Server**: `production`
- **Signature**: `def read_customer(id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `id`
- **Params**: `id` — path
- **Returns (parsed)**: `CustomerResponse`
- **Returns (raw)**: `ApiResult[CustomerResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `CustomerResponse` | `maxio_advanced_billing/models/customer_response.py` |

### client.customers.read_customer_by_reference

- **Route**: `GET /customers/lookup.json`
- **Server**: `production`
- **Signature**: `def read_customer_by_reference(reference: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `reference`
- **Params**: `reference` — query
- **Returns (parsed)**: `CustomerResponse`
- **Returns (raw)**: `ApiResult[CustomerResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `CustomerResponse` | `maxio_advanced_billing/models/customer_response.py` |

### client.customers.update_customer

- **Route**: `PUT /customers/{id}.json`
- **Server**: `production`
- **Signature**: `def update_customer(id: int, *, body: UpdateCustomerRequest | UpdateCustomerRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `id`
- **Params**: `id` — path · `body` — JSON body
- **Returns (parsed)**: `CustomerResponse`
- **Returns (raw)**: `ApiResult[CustomerResponse, UpdateCustomerErrorBody]`
- **Error**: `UpdateCustomerErrorBody` — **Case A (typed)**
- **Error arms**: `CustomerErrorResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `UpdateCustomerRequest` | `maxio_advanced_billing/models/update_customer_request.py` |
| `UpdateCustomerRequestDict` | `maxio_advanced_billing/models/update_customer_request.py` |
| `CustomerResponse` | `maxio_advanced_billing/models/customer_response.py` |
| `UpdateCustomerErrorBody` | `maxio_advanced_billing/errors/update_customer_error.py` |
| `CustomerErrorResponse1` | `maxio_advanced_billing/models/customer_error_response1.py` |

