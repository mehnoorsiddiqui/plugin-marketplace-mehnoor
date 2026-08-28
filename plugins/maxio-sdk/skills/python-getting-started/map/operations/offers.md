<!-- Generated file — do not edit; regenerated with the SDK. -->

# Offers — operations

Accessor: `client.offers` · Source: `maxio_advanced_billing/apis/offers.py` · 5 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded, and an operation with no table mentions nothing but builtins and those.

### client.offers.archive_offer

- **Route**: `PUT /offers/{offer_id}/archive.json`
- **Server**: `production`
- **Signature**: `def archive_offer(offer_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `offer_id`
- **Params**: `offer_id` — path
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, RawError]`
- **Error**: `RawError` — **Case B**

### client.offers.create_offer

- **Route**: `POST /offers.json`
- **Server**: `production`
- **Signature**: `def create_offer(*, body: CreateOfferRequest | CreateOfferRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `body` — JSON body
- **Returns (parsed)**: `OfferResponse`
- **Returns (raw)**: `ApiResult[OfferResponse, CreateOfferErrorBody]`
- **Error**: `CreateOfferErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorArrayMapResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateOfferRequest` | `maxio_advanced_billing/models/create_offer_request.py` |
| `CreateOfferRequestDict` | `maxio_advanced_billing/models/create_offer_request.py` |
| `OfferResponse` | `maxio_advanced_billing/models/offer_response.py` |
| `CreateOfferErrorBody` | `maxio_advanced_billing/errors/create_offer_error.py` |
| `ErrorArrayMapResponse1` | `maxio_advanced_billing/models/error_array_map_response1.py` |

### client.offers.list_offers

- **Route**: `GET /offers.json`
- **Server**: `production`
- **Signature**: `def list_offers(*, page: int | None = 1, per_page: int | None = 20, include_archived: bool | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `page` — query · `per_page` — query · `include_archived` — query
- **Returns (parsed)**: `ListOffersResponse`
- **Returns (raw)**: `ApiResult[ListOffersResponse, ListOffersErrorBody]`
- **Error**: `ListOffersErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ListOffersResponse` | `maxio_advanced_billing/models/list_offers_response.py` |
| `ListOffersErrorBody` | `maxio_advanced_billing/errors/list_offers_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.offers.read_offer

- **Route**: `GET /offers/{offer_id}.json`
- **Server**: `production`
- **Signature**: `def read_offer(offer_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `offer_id`
- **Params**: `offer_id` — path
- **Returns (parsed)**: `OfferResponse`
- **Returns (raw)**: `ApiResult[OfferResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `OfferResponse` | `maxio_advanced_billing/models/offer_response.py` |

### client.offers.unarchive_offer

- **Route**: `PUT /offers/{offer_id}/unarchive.json`
- **Server**: `production`
- **Signature**: `def unarchive_offer(offer_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `offer_id`
- **Params**: `offer_id` — path
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, RawError]`
- **Error**: `RawError` — **Case B**

