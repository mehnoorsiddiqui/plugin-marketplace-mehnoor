<!-- Generated file — do not edit; regenerated with the SDK. -->

# Sites — operations

Accessor: `client.sites` · Source: `maxio_advanced_billing/apis/sites.py` · 3 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.sites.clear_site

- **Route**: `POST /sites/clear_data.json`
- **Server**: `production`
- **Signature**: `def clear_site(*, cleanup_scope: CleanupScopeOrStr | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `cleanup_scope` — query
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `CleanupScopeOrStr` | `maxio_advanced_billing/models/enums/cleanup_scope.py` |

### client.sites.list_chargify_js_public_keys

- **Route**: `GET /chargify_js_keys.json`
- **Server**: `production`
- **Signature**: `def list_chargify_js_public_keys(*, page: int | None = 1, per_page: int | None = 20, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `page` — query · `per_page` — query
- **Returns (parsed)**: `ListPublicKeysResponse`
- **Returns (raw)**: `ApiResult[ListPublicKeysResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `ListPublicKeysResponse` | `maxio_advanced_billing/models/list_public_keys_response.py` |

### client.sites.read_site

- **Route**: `GET /site.json`
- **Server**: `production`
- **Signature**: `def read_site(*, request_options: RequestOptionsOrDict | None = None)`
- **Returns (parsed)**: `SiteResponse`
- **Returns (raw)**: `ApiResult[SiteResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SiteResponse` | `maxio_advanced_billing/models/site_response.py` |

