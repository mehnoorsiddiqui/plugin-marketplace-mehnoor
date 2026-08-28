<!-- Generated file — do not edit; regenerated with the SDK. -->

# CustomFields — operations

Accessor: `client.custom_fields` · Source: `maxio_advanced_billing/apis/custom_fields.py` · 9 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.custom_fields.create_metadata

- **Route**: `POST /{resource_type}/{resource_id}/metadata.json`
- **Server**: `production`
- **Signature**: `def create_metadata(resource_type: ResourceTypeOrStr, resource_id: int, *, body: CreateMetadataRequest | CreateMetadataRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `resource_type`, `resource_id`
- **Params**: `resource_type` — path · `resource_id` — path · `body` — JSON body
- **Returns (parsed)**: `list[Metadata]`
- **Returns (raw)**: `ApiResult[list[Metadata], CreateMetadataErrorBody]`
- **Error**: `CreateMetadataErrorBody` — **Case A (typed)**
- **Error arms**: `SingleErrorResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ResourceTypeOrStr` | `maxio_advanced_billing/models/enums/resource_type.py` |
| `CreateMetadataRequest` | `maxio_advanced_billing/models/create_metadata_request.py` |
| `CreateMetadataRequestDict` | `maxio_advanced_billing/models/create_metadata_request.py` |
| `Metadata` | `maxio_advanced_billing/models/metadata.py` |
| `CreateMetadataErrorBody` | `maxio_advanced_billing/errors/create_metadata_error.py` |
| `SingleErrorResponse1` | `maxio_advanced_billing/models/single_error_response1.py` |

### client.custom_fields.create_metafields

- **Route**: `POST /{resource_type}/metafields.json`
- **Server**: `production`
- **Signature**: `def create_metafields(resource_type: ResourceTypeOrStr, *, body: CreateMetafieldsRequest | CreateMetafieldsRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `resource_type`
- **Params**: `resource_type` — path · `body` — JSON body
- **Returns (parsed)**: `list[Metafield]`
- **Returns (raw)**: `ApiResult[list[Metafield], CreateMetafieldsErrorBody]`
- **Error**: `CreateMetafieldsErrorBody` — **Case A (typed)**
- **Error arms**: `SingleErrorResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ResourceTypeOrStr` | `maxio_advanced_billing/models/enums/resource_type.py` |
| `CreateMetafieldsRequest` | `maxio_advanced_billing/models/create_metafields_request.py` |
| `CreateMetafieldsRequestDict` | `maxio_advanced_billing/models/create_metafields_request.py` |
| `Metafield` | `maxio_advanced_billing/models/metafield.py` |
| `CreateMetafieldsErrorBody` | `maxio_advanced_billing/errors/create_metafields_error.py` |
| `SingleErrorResponse1` | `maxio_advanced_billing/models/single_error_response1.py` |

### client.custom_fields.delete_metadata

- **Route**: `DELETE /{resource_type}/{resource_id}/metadata.json`
- **Server**: `production`
- **Signature**: `def delete_metadata(resource_type: ResourceTypeOrStr, resource_id: int, *, name: str | None = None, names: list[str] | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `resource_type`, `resource_id`
- **Params**: `resource_type` — path · `resource_id` — path · `name` — query · `names` — query
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, DeleteMetadataErrorBody]`
- **Error**: `DeleteMetadataErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `ResourceTypeOrStr` | `maxio_advanced_billing/models/enums/resource_type.py` |
| `DeleteMetadataErrorBody` | `maxio_advanced_billing/errors/delete_metadata_error.py` |

### client.custom_fields.delete_metafield

- **Route**: `DELETE /{resource_type}/metafields.json`
- **Server**: `production`
- **Signature**: `def delete_metafield(resource_type: ResourceTypeOrStr, *, name: str | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `resource_type`
- **Params**: `resource_type` — path · `name` — query
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, DeleteMetafieldErrorBody]`
- **Error**: `DeleteMetafieldErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `ResourceTypeOrStr` | `maxio_advanced_billing/models/enums/resource_type.py` |
| `DeleteMetafieldErrorBody` | `maxio_advanced_billing/errors/delete_metafield_error.py` |

### client.custom_fields.list_metadata

- **Route**: `GET /{resource_type}/{resource_id}/metadata.json`
- **Server**: `production`
- **Signature**: `def list_metadata(resource_type: ResourceTypeOrStr, resource_id: int, *, page: int | None = 1, per_page: int | None = 20, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `resource_type`, `resource_id`
- **Params**: `resource_type` — path · `resource_id` — path · `page` — query · `per_page` — query
- **Returns (parsed)**: `PaginatedMetadata`
- **Returns (raw)**: `ApiResult[PaginatedMetadata, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ResourceTypeOrStr` | `maxio_advanced_billing/models/enums/resource_type.py` |
| `PaginatedMetadata` | `maxio_advanced_billing/models/paginated_metadata.py` |

### client.custom_fields.list_metadata_for_resource_type

- **Route**: `GET /{resource_type}/metadata.json`
- **Server**: `production`
- **Signature**: `def list_metadata_for_resource_type(resource_type: ResourceTypeOrStr, *, page: int | None = 1, per_page: int | None = 20, date_field: BasicDateFieldOrStr | None = None, start_date: Date | None = None, end_date: Date | None = None, start_datetime: RFC3339DateTime | None = None, end_datetime: RFC3339DateTime | None = None, with_deleted: bool | None = None, resource_ids: list[int] | None = None, direction: SortingDirectionOrStr | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `resource_type`
- **Params**: `resource_type` — path · `page` — query · `per_page` — query · `date_field` — query · `start_date` — query · `end_date` — query · `start_datetime` — query · `end_datetime` — query · `with_deleted` — query · `resource_ids` — query · `direction` — query
- **Returns (parsed)**: `PaginatedMetadata`
- **Returns (raw)**: `ApiResult[PaginatedMetadata, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ResourceTypeOrStr` | `maxio_advanced_billing/models/enums/resource_type.py` |
| `BasicDateFieldOrStr` | `maxio_advanced_billing/models/enums/basic_date_field.py` |
| `SortingDirectionOrStr` | `maxio_advanced_billing/models/enums/sorting_direction.py` |
| `PaginatedMetadata` | `maxio_advanced_billing/models/paginated_metadata.py` |

### client.custom_fields.list_metafields

- **Route**: `GET /{resource_type}/metafields.json`
- **Server**: `production`
- **Signature**: `def list_metafields(resource_type: ResourceTypeOrStr, *, name: str | None = None, page: int | None = 1, per_page: int | None = 20, direction: SortingDirectionOrStr | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `resource_type`
- **Params**: `resource_type` — path · `name` — query · `page` — query · `per_page` — query · `direction` — query
- **Returns (parsed)**: `ListMetafieldsResponse`
- **Returns (raw)**: `ApiResult[ListMetafieldsResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ResourceTypeOrStr` | `maxio_advanced_billing/models/enums/resource_type.py` |
| `SortingDirectionOrStr` | `maxio_advanced_billing/models/enums/sorting_direction.py` |
| `ListMetafieldsResponse` | `maxio_advanced_billing/models/list_metafields_response.py` |

### client.custom_fields.update_metadata

- **Route**: `PUT /{resource_type}/{resource_id}/metadata.json`
- **Server**: `production`
- **Signature**: `def update_metadata(resource_type: ResourceTypeOrStr, resource_id: int, *, body: UpdateMetadataRequest | UpdateMetadataRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `resource_type`, `resource_id`
- **Params**: `resource_type` — path · `resource_id` — path · `body` — JSON body
- **Returns (parsed)**: `list[Metadata]`
- **Returns (raw)**: `ApiResult[list[Metadata], UpdateMetadataErrorBody]`
- **Error**: `UpdateMetadataErrorBody` — **Case A (typed)**
- **Error arms**: `SingleErrorResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ResourceTypeOrStr` | `maxio_advanced_billing/models/enums/resource_type.py` |
| `UpdateMetadataRequest` | `maxio_advanced_billing/models/update_metadata_request.py` |
| `UpdateMetadataRequestDict` | `maxio_advanced_billing/models/update_metadata_request.py` |
| `Metadata` | `maxio_advanced_billing/models/metadata.py` |
| `UpdateMetadataErrorBody` | `maxio_advanced_billing/errors/update_metadata_error.py` |
| `SingleErrorResponse1` | `maxio_advanced_billing/models/single_error_response1.py` |

### client.custom_fields.update_metafield

- **Route**: `PUT /{resource_type}/metafields.json`
- **Server**: `production`
- **Signature**: `def update_metafield(resource_type: ResourceTypeOrStr, *, body: UpdateMetafieldsRequest | UpdateMetafieldsRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `resource_type`
- **Params**: `resource_type` — path · `body` — JSON body
- **Returns (parsed)**: `list[Metafield]`
- **Returns (raw)**: `ApiResult[list[Metafield], UpdateMetafieldErrorBody]`
- **Error**: `UpdateMetafieldErrorBody` — **Case A (typed)**
- **Error arms**: `SingleErrorResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ResourceTypeOrStr` | `maxio_advanced_billing/models/enums/resource_type.py` |
| `UpdateMetafieldsRequest` | `maxio_advanced_billing/models/update_metafields_request.py` |
| `UpdateMetafieldsRequestDict` | `maxio_advanced_billing/models/update_metafields_request.py` |
| `Metafield` | `maxio_advanced_billing/models/metafield.py` |
| `UpdateMetafieldErrorBody` | `maxio_advanced_billing/errors/update_metafield_error.py` |
| `SingleErrorResponse1` | `maxio_advanced_billing/models/single_error_response1.py` |

