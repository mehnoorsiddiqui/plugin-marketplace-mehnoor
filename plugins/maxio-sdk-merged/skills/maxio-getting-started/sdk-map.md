# SDK map — Maxio Advanced Billing (.NET)

> A generated table-of-contents for this SDK. Consult this map and its sub-pages to learn signatures, error
> types, enum values, and server/auth wiring **by lookup** — open a source file only for a full method/model
> body the map doesn't carry. The compiler is the backstop: a wrong name fails to build.

| | |
|---|---|
| SDK display name | Maxio Advanced Billing (formerly Chargify) — sample SDK |
| Root namespace/module | `MaxioAdvancedBilling` |
<!-- gen:stamp -->
| NuGet package | `AsadAli.AdvancedBilling.Sdk` |
| Target framework(s) | `netstandard2.0` (C# `LangVersion 14`, `Nullable enable`) |
| Source commit (spec stamp) | `15db14b` (`15db14b2e663ebe9e957e061bd67634630429035`, tagged `v1.0.2`) |
<!-- /gen:stamp -->
| Generator | APIMatic |
| Repo | https://github.com/asadali214/advanced-billing-sample-sdk |

Staleness check: if the SDK is regenerated, the source commit/tag stamp above changes. If a lookup here fails to
compile, trust the compiler and re-read the source file linked in the row.

---

## Getting a client

```csharp
using MaxioAdvancedBilling;
using MaxioAdvancedBilling.Core.Authentication.Basic;
using MaxioAdvancedBilling.Servers; // ServerEnvironment lives here

var options = new MaxioAdvancedBillingClientOptions
{
    // Basic auth: Username = your Maxio/Chargify API key, Password = the literal "x"
    BasicAuth = new BasicAuthCredentials { Username = "<api_key>", Password = "x" },
    // Environment selects US (default) or EU hosting; see Servers & auth below
    Environment = ServerEnvironment.Us,
};
var client = new MaxioAdvancedBillingClient(httpClient, options); // httpClient: System.Net.Http.HttpClient
```

DI alternative (`ServiceCollectionExtensions.cs`):

```csharp
services.AddMaxioAdvancedBillingClient(o =>
{
    o.BasicAuth = new BasicAuthCredentials { Username = "<api_key>", Password = "x" };
});
```

Every API group is a property on the client (e.g. `client.Customers`, `client.Subscriptions`). Source:
`MaxioAdvancedBillingClient.cs`. The only constructor is
`MaxioAdvancedBillingClient(HttpClient httpClient, MaxioAdvancedBillingClientOptions options)`.

All `MaxioAdvancedBillingClientOptions` properties (source: `MaxioAdvancedBillingClientOptions.cs`):

| Property | Type |
|---|---|
| `Environment` | `ServerEnvironment` |
| `Retry` | `RetryOptions` |
| `Server` | `ServerOptions` |
| `BasicAuth` | `BasicAuthCredentials?` |

`RetryOptions` members (namespace `MaxioAdvancedBilling.Core.Configuration` — add `using
MaxioAdvancedBilling.Core.Configuration;`; source: `Core/Configuration/RetryOptions.cs`; all members are
`required`, so build a full instance or start from `RetryOptions.Default()`):

| Member | Type |
|---|---|
| `StatusCodesToRetry` | `IReadOnlyList<HttpStatusCode>` |
| `HttpMethodsToRetry` | `IReadOnlyList<HttpMethod>` |
| `MaxRetries` | `int` |
| `Delay` | `TimeSpan` |
| `Timeout` | `TimeSpan?` |
| `BackOffFactor` | `int` |
| `UseExponentialBackoff` | `bool` |
| `MaxJitter` | `TimeSpan` |
| `OnRetry` | `Action<RetryAttempt>?` |

---

## Error-handling model (read once — applies to every operation)

Operations are **throw-based**. On an error status the SDK throws `SdkException<TError>`
(`Core/Exceptions/SdkException.cs`) exposing `.Error` of type `TError`. There are two cases:

- **Case A — typed error.** `TError` is a generated `…Error : ApiError` class with status-specific
  `TryGet…(out …)` accessors (returns `true` when that shape is present) plus the inherited
  `TryGetRawError(out RawError)` fallback. The per-operation rows name the exact `TryGet…` methods and the HTTP
  status each maps to.
- **Case B — raw error.** `TError` is `RawError` (`Core/ErrorResponse/RawError.cs`): `StatusCode`,
  `ReadAsString()`, `ReadAsJson<T>()`, `ReadAsBytes()`.

<!-- gen:error-core -->
Core error types (`Core/ErrorResponse/`) — public members with their **declared types**, verbatim from source:

| Type | Public members | Source |
|---|---|---|
| `ApiError` — abstract base of all 163 typed error classes in `Errors/` | `TryGetRawError(out RawError error): bool` | `Core/ErrorResponse/ApiError.cs` |
| `RawError` | `StatusCode: HttpStatusCode` · `ReadAsBytes(): ReadOnlyMemory<byte>` · `ReadAsString(): string` · `ReadAsJson<T>(): T?` | `Core/ErrorResponse/RawError.cs` |

Typed-error payload shapes (the `out` types in each operation page's error-accessor cells) are ordinary records/unions: field names, declared types, and JSON wire names live on the records pages / `unions.md` like any other model.
<!-- /gen:error-core -->

```csharp
try { var resp = await client.Customers.CreateCustomer(body); }
catch (SdkException<CreateCustomerError> ex)          // Case A
{
    if (ex.Error.TryGetCustomerErrorResponse1(out var e422)) { /* handle 422 */ }
    else if (ex.Error.TryGetRawError(out var raw))    { /* other statuses */ }
}
catch (SdkException<RawError> ex)                     // Case B
{
    var status = ex.Error.StatusCode;
    var body   = ex.Error.ReadAsString();
}
```

**No-throw ("`…Result`") variants: absent across this SDK** — every operation is throw-only.
Of **247 operations**, **163 are Case A (typed)** and **84 are Case B (raw)**.

---

## Operations — by controller (33 groups, 247 operations)

Each links to a sub-page with one row per operation (HTTP, signature with must-pass-explicitly params, return
type, error Case A/B + accessors, pagination).

| Controller (`client.X`) | Ops | Page |
|---|---:|---|
| `Subscriptions` | 12 | [map/operations/Subscriptions.md](map/operations/Subscriptions.md) |
| `SubscriptionComponents` | 17 | [map/operations/SubscriptionComponents.md](map/operations/SubscriptionComponents.md) |
| `SubscriptionGroups` | 9 | [map/operations/SubscriptionGroups.md](map/operations/SubscriptionGroups.md) |
| `SubscriptionGroupStatus` | 4 | [map/operations/SubscriptionGroupStatus.md](map/operations/SubscriptionGroupStatus.md) |
| `SubscriptionGroupInvoiceAccount` | 4 | [map/operations/SubscriptionGroupInvoiceAccount.md](map/operations/SubscriptionGroupInvoiceAccount.md) |
| `SubscriptionInvoiceAccount` | 7 | [map/operations/SubscriptionInvoiceAccount.md](map/operations/SubscriptionInvoiceAccount.md) |
| `SubscriptionStatus` | 10 | [map/operations/SubscriptionStatus.md](map/operations/SubscriptionStatus.md) |
| `SubscriptionNotes` | 5 | [map/operations/SubscriptionNotes.md](map/operations/SubscriptionNotes.md) |
| `SubscriptionProducts` | 2 | [map/operations/SubscriptionProducts.md](map/operations/SubscriptionProducts.md) |
| `SubscriptionRenewals` | 11 | [map/operations/SubscriptionRenewals.md](map/operations/SubscriptionRenewals.md) |
| `Invoices` | 17 | [map/operations/Invoices.md](map/operations/Invoices.md) |
| `ProformaInvoices` | 10 | [map/operations/ProformaInvoices.md](map/operations/ProformaInvoices.md) |
| `AdvanceInvoice` | 3 | [map/operations/AdvanceInvoice.md](map/operations/AdvanceInvoice.md) |
| `Coupons` | 14 | [map/operations/Coupons.md](map/operations/Coupons.md) |
| `Components` | 12 | [map/operations/Components.md](map/operations/Components.md) |
| `ComponentPricePoints` | 12 | [map/operations/ComponentPricePoints.md](map/operations/ComponentPricePoints.md) |
| `Products` | 6 | [map/operations/Products.md](map/operations/Products.md) |
| `ProductPricePoints` | 11 | [map/operations/ProductPricePoints.md](map/operations/ProductPricePoints.md) |
| `ProductFamilies` | 4 | [map/operations/ProductFamilies.md](map/operations/ProductFamilies.md) |
| `Customers` | 7 | [map/operations/Customers.md](map/operations/Customers.md) |
| `PaymentProfiles` | 12 | [map/operations/PaymentProfiles.md](map/operations/PaymentProfiles.md) |
| `CustomFields` | 9 | [map/operations/CustomFields.md](map/operations/CustomFields.md) |
| `Offers` | 5 | [map/operations/Offers.md](map/operations/Offers.md) |
| `ReasonCodes` | 5 | [map/operations/ReasonCodes.md](map/operations/ReasonCodes.md) |
| `ReferralCodes` | 1 | [map/operations/ReferralCodes.md](map/operations/ReferralCodes.md) |
| `SalesCommissions` | 3 | [map/operations/SalesCommissions.md](map/operations/SalesCommissions.md) |
| `Sites` | 3 | [map/operations/Sites.md](map/operations/Sites.md) |
| `Events` | 3 | [map/operations/Events.md](map/operations/Events.md) |
| `EventsBasedBillingSegments` | 6 | [map/operations/EventsBasedBillingSegments.md](map/operations/EventsBasedBillingSegments.md) |
| `Insights` | 4 | [map/operations/Insights.md](map/operations/Insights.md) |
| `BillingPortal` | 4 | [map/operations/BillingPortal.md](map/operations/BillingPortal.md) |
| `Webhooks` | 6 | [map/operations/Webhooks.md](map/operations/Webhooks.md) |
| `ApiExports` | 9 | [map/operations/ApiExports.md](map/operations/ApiExports.md) |

---

## Models

<!-- gen:models-table -->
| Group | Count | Page |
|---|---:|---|
| Records (plain `record` data models) | 555 | [`AccountBalance` … `CreateSegmentRequest`](map/models/records-1-Ac-Cr.md) · [`CreateSubscription` … `NetTerms`](map/models/records-2-Cr-Ne.md) · [`Offer` … `SubscriptionComponentSubscription`](map/models/records-3-Of-Su.md) · [`SubscriptionCustomPrice` … `WebhookResponse`](map/models/records-4-Su-We.md) |
| Unions (`OneOf` / `AnyOf`) — variant factories + `TryGet…` | 7 + 83 | [map/models/unions.md](map/models/unions.md) |
| Enums (`StringEnum<T>` / `IntEnum<T>`) — literal C# member names + wire values | 98 | [map/models/enums.md](map/models/enums.md) |
<!-- /gen:models-table -->

Model conventions: records are immutable with `init`-only setters; `required` properties must be set in the
object initializer; nullable (`T?`) properties are optional. Each record field is listed as
`CSharpName (wire_name): Type` — the parenthesized name is the JSON wire name (`[JsonPropertyName]`).
Unions wrap `Optional<T>` variants — construct via a static factory or implicit
conversion, read back via `TryGet…(out …)`. Enums are **not** C# enums — build with `Type.FromValue("wire")`
or the static members (enums.md lists the literal member names: `CollectionMethod.Invoice`, not
`CollectionMethod.invoice`).

<!-- gen:namespaces -->
Namespaces by content type (add `using` accordingly):

| Contents | Namespace(s) |
|---|---|
| Client & options (root) | `MaxioAdvancedBilling` |
| Operation controllers (`Api/`) | `MaxioAdvancedBilling.Api` |
| Records (`Models/`) | `MaxioAdvancedBilling.Models` |
| Enums (`Models/Enums/`) | `MaxioAdvancedBilling.Models.Enums` |
| Unions (`Models/AnyOf/`, `Models/OneOf/`) | `MaxioAdvancedBilling.Models.AnyOf` · `MaxioAdvancedBilling.Models.OneOf` |
| Error classes (`Errors/`) | `MaxioAdvancedBilling.Errors` |
<!-- /gen:namespaces -->

---

## Servers & auth

**Auth — Basic only.** Set `options.BasicAuth = new BasicAuthCredentials { Username = "<api_key>", Password = "x" }`.
Convention: **`Username` = your Maxio/Chargify API key, `Password` = the literal string `"x"`**.
Source: `MaxioAdvancedBillingClientOptions.cs`, `Core/Authentication/Basic/BasicAuthCredentials.cs`.

**Environments.** `options.Environment` is a `ServerEnvironment` (`Servers/ServerEnvironment.cs`):

| Environment | Value | Hosting |
|---|---|---|
| `ServerEnvironment.Us` *(default)* | `US` | US-hosted (default for most accounts) |
| `ServerEnvironment.Eu` | `EU` | EU-hosted (only if your account requested EU hosting) |

**Two server groups** (most operations use Production; only `SubscriptionComponents` event-ingest endpoints use
Ebb). Base-URL templates and the **override points** (`options.Server.…`):

| Group | US base-URL template | EU base-URL template | Override point |
|---|---|---|---|
| Production | `https://{site}.chargify.com` | `https://{site}.ebilling.maxio.com` | `options.Server.Production.Us.BaseUrl` / `.Us.Site` (and `.Eu.*`) |
| Ebb (events) | `https://events.chargify.com/{site}` | `https://events.chargify.com/{site}` | `options.Server.Ebb.Us.BaseUrl` / `.Us.Site` (and `.Eu.*`) |

`{site}` defaults to `subdomain` — set `options.Server.Production.Us.Site = "your-subdomain"`.
**To redirect to a mock/dev host**, override `BaseUrl` on the relevant group, e.g.
`options.Server.Production.Us.BaseUrl = "http://localhost:8080"`. Sources: `Server.cs`, `ServerOptions.cs`,
`Servers/ProductionOptions.cs`, `Servers/EbbOptions.cs`.

Retry/resilience is configurable via `options.Retry` (`RetryOptions`, backed by Polly).
