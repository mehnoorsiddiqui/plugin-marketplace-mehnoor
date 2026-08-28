<!-- Generated file — do not edit; regenerated with the SDK. -->

# SubscriptionProducts — operations

Accessor: `client.subscription_products` · Source: `maxio_advanced_billing/apis/subscription_products.py` · 2 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.subscription_products.migrate_subscription_product

- **Route**: `POST /subscriptions/{subscription_id}/migrations.json`
- **Server**: `production`
- **Signature**: `def migrate_subscription_product(subscription_id: int, *, body: SubscriptionProductMigrationRequest | SubscriptionProductMigrationRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionResponse`
- **Returns (raw)**: `ApiResult[SubscriptionResponse, MigrateSubscriptionProductErrorBody]`
- **Error**: `MigrateSubscriptionProductErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `SubscriptionProductMigrationRequest` | `maxio_advanced_billing/models/subscription_product_migration_request.py` |
| `SubscriptionProductMigrationRequestDict` | `maxio_advanced_billing/models/subscription_product_migration_request.py` |
| `SubscriptionResponse` | `maxio_advanced_billing/models/subscription_response.py` |
| `MigrateSubscriptionProductErrorBody` | `maxio_advanced_billing/errors/migrate_subscription_product_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_products.preview_subscription_product_migration

- **Route**: `POST /subscriptions/{subscription_id}/migrations/preview.json`
- **Server**: `production`
- **Signature**: `def preview_subscription_product_migration(subscription_id: int, *, body: SubscriptionMigrationPreviewRequest | SubscriptionMigrationPreviewRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionMigrationPreviewResponse`
- **Returns (raw)**: `ApiResult[SubscriptionMigrationPreviewResponse, PreviewSubscriptionProductMigrationErrorBody]`
- **Error**: `PreviewSubscriptionProductMigrationErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `SubscriptionMigrationPreviewRequest` | `maxio_advanced_billing/models/subscription_migration_preview_request.py` |
| `SubscriptionMigrationPreviewRequestDict` | `maxio_advanced_billing/models/subscription_migration_preview_request.py` |
| `SubscriptionMigrationPreviewResponse` | `maxio_advanced_billing/models/subscription_migration_preview_response.py` |
| `PreviewSubscriptionProductMigrationErrorBody` | `maxio_advanced_billing/errors/preview_subscription_product_migration_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

