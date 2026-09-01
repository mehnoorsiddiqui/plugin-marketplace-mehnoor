---
name: "typescript-paypal-getting-started"
description: "PayPal TypeScript SDK identity and lookup layer (TypeScript/JavaScript only) — install, the single import specifier, the one server environment and the base-URL knob, the auth pattern, the SDK map that ships inside the installed package (`sdk-map.md` + `map/operations/`) and how to traverse it, and the file table naming the one source file owning each fact the map leaves to the source. Load this before answering any PayPal TypeScript SDK contract question or writing any SDK code."
---

# Getting started with the PayPal TypeScript SDK

> **Who this skill is for.** This is the **lookup layer** for anyone writing PayPal TypeScript SDK code — it is yours to follow directly and fully. Ground every contract fact here (in the SDK map, and in the source files it names) rather than in recall, and carry those facts onto a contract sheet before you implement. Load `typescript-integrate-paypal` for the workflow that wraps this skill.

This is the **SDK-specific** entry point. For general patterns that apply to any APIMatic-generated TypeScript SDK (client construction, auth, calling endpoints, models, error handling, resilience, testing), see the companion API-agnostic skills: `typescript-client-initialization`, `typescript-authentication`, `typescript-calling-endpoints`, `typescript-models`, `typescript-error-handling`, `typescript-configuration-resilience` and `typescript-testing`.

**This page and those companion skills are complementary — load both.** This page is authoritative for the SDK's *identity and surface* (what to install, what to import, the resources, which file owns which fact); the companion skills are the *usage layer* on top — the best-practice way to call each piece and the gotchas a signature cannot show. Reading a file in the installed package does not remove the need to load the skill for that step, so at each step below, load the companion *and* confirm names against the installed package.

## SDK identity

Verified against `package.json` and `sdk-map.md` of the generated package at version `2.29`. **Re-verify after a version bump** — this page is a snapshot, not a live read.

| Fact | Value |
| --- | --- |
| API | PayPal Server SDK |
| Package name (what you install, and what you import) | `pay-pal-server-sdk` — **not on npm**; installed from source (see *Install*). Unlike the Python SDK, the two names are the same string |
| Import specifier | `pay-pal-server-sdk` — the package root is the **only** entry; deep imports do not resolve |
| Version | `2.29` (API spec version `2.29`) |
| Client class | `PayPalServerSdkClient` (`src/client.ts`) — one class, no sync/async split |
| Options type | `ClientOptions`, with `DEFAULT_CLIENT_OPTIONS` beside it (`src/client-options.ts`) |
| Client construction | `new PayPalServerSdkClient(clientOptions: Partial<ClientOptions> = {})` — **every** field is optional, so `new PayPalServerSdkClient()` compiles. Fields: `serverEnvironment` · `serverOptions` · `timeout` (default `60_000` ms) · `fetch` · `oauth2` · `oauth2Strategy` |
| Auth | **OAuth 2.0** client credentials — set `oauth2` |
| Environments | **one environment only** — `ServerEnvironment.Sandbox`, the default. There is no live/production member; reaching live PayPal means overriding the base URL (see *Environments*) |
| Base-URL config | `serverOptions.default.sandbox.baseUrl` (`src/servers.ts`), defaulting to `https://api-m.sandbox.paypal.com` |
| Node floor | `>=20` (`engines.node`) |
| Runtime dependency | `zod` (`^3.25.0 \|\| ^4.0.0`), imported as `zod/v4-mini` — the only one |
| Module format | dual ESM + CommonJS folder dialects (`dist/esm`, `dist/commonjs`) behind one export |
| Typing | the package ships its own `.d.ts` and is generated under strict TypeScript. Callers get full inference — **a type error against this SDK is a real contract violation, not noise** |
| Surface | 40 operations across 5 resources · 284 models · 87 open enums · 0 undiscriminated unions · 40 per-operation error subclasses |

The table above is **orientation, not a copy-paste recipe** — it gives you the names and facts (install, import specifier, the auth *pattern*, the base-URL knob), while the actual integration code comes from the companion skills. Load each one as you reach its step (see **Integration workflow** below) and confirm its types against the installed package.

## Install — from source

This SDK is not published to npm, so there is no registry install for it. It ships TypeScript sources and **compiles to `dist/` before it can be referenced** — build it once in the SDK folder, then depend on it by path from the consuming project:

```bash
# in the SDK folder:
npm install && npm run build
# then, from the consuming project:
npm install https://github.com/mehnoorsiddiqui/paypal-ts-v4.git
```

Do not vendor its `src/` into your project, point `tsconfig` `paths` at a throwaway clone, or import from `dist/` directly. Installing the package properly is what makes the `exports` map, the shipped `.d.ts` chain and the dual-dialect resolution behave the way the SDK expects — **and it is what puts the SDK map inside `node_modules`, which is where every lookup below reads it from**. Requires Node 20 or newer.

## Imports — one entry, and only one

Unlike Python, TypeScript here has no module split to learn: **every** public name is re-exported from the package root — the client, `ClientOptions`, `ServerEnvironment`, all 284 model types with the schema value beside each, all 87 enums, the error classes, and the runtime types (`ApiPromise`, `ApiResult`, `RequestOptions`, `ErrorPayload`, `Declared`, `Schema`, `EnumSchema`, `Encoded`).

```ts
import { PayPalServerSdkClient, ServerEnvironment, ResponseError, PayPalServerSdkError } from "pay-pal-server-sdk";
import type { ClientOptions, Order } from "pay-pal-server-sdk";
```

Four things the specifier alone will not tell you:

- **Deep imports do not resolve.** The `exports` map exposes `.` and `./package.json` and nothing else, so `pay-pal-server-sdk/models/order` fails (`TS2307`) even though the file exists in the shipped `src/`. Every `Source` path on the SDK map is where to **read** a shape, never what to import.
- **⚠ The SDK exports a model type literally named `Error`** (`src/models/error.ts`, schema `errorSchema`) — it is PayPal's error body, and it is what the typed error arms of 21 operations carry. Import it unaliased and it **shadows the global `Error`** for the rest of the file. Alias it: `import type { Error as PayPalError } from "pay-pal-server-sdk"`.
- **From CommonJS**, the typed spelling is `import sdk = require("pay-pal-server-sdk")`. A plain `require` destructure runs but yields `any`.
- **`instanceof` is reliable within one dialect.** A process that loads both (`import` in one file, `require` in another) gets two independent copies of every error class, and `instanceof` across that boundary is `false` — narrow on `err.kind` / `err.payload.kind` / `err.name` there.

Under `verbatimModuleSyntax`, names carrying no runtime value (`ClientOptions`, every model type) must be imported with `import type`. Under `exactOptionalPropertyTypes`, **omit or spread** an absent optional field rather than assigning `undefined` to it.

## Environments — there is one, and it is sandbox

⚠ **This is the single most consequential fact on this page.** `ServerEnvironment` is a `const` object with a derived union type — not a TypeScript `enum` — and unlike the model enums it is **closed**. It declares exactly one member:

```ts
export const ServerEnvironment = { Sandbox: "sandbox" } as const;   // src/servers.ts
```

There is **no `Production` / `Live` member**, and `DEFAULT_CLIENT_OPTIONS.serverEnvironment` is `ServerEnvironment.Sandbox`. One server group (`default`), one base URL:

| Group | Environment | Base URL | Override at |
| --- | --- | --- | --- |
| `default` | `sandbox` *(default)* | `https://api-m.sandbox.paypal.com` | `serverOptions.default.sandbox.baseUrl` |

Consequences to state on every contract sheet that touches configuration:

- Constructing the client with no options talks to **PayPal sandbox**, silently. Nothing in the type system marks that as a test host.
- **Reaching live PayPal is a `serverOptions` override, not an environment value** — `serverOptions: { default: { sandbox: { baseUrl: "https://api-m.paypal.com" } } }`. Note the key is still `sandbox`: the environment name and the host it points at are now decoupled, which is exactly the shape of configuration that gets a live secret pointed at sandbox or the reverse. Make the deployment's intent explicit where the client is built, and verify it.
- The **token endpoint follows the same base URL** (`default` + `/v1/oauth2/token`), so it always tracks the override — you never configure it separately.
- An override merges with the built-in default **per group-and-environment pair, key by key**; a `baseUrl` override replaces the template verbatim, and templates expand per request rather than once at construction.
- An environment value the SDK does not know throws `SdkError` **synchronously out of the operation method** at the first call — not at construction — so `try`/`await` catches it but `.asApiResult()` and `.catch()` never see it.

## Auth pattern (one scheme)

OAuth 2.0 client credentials, exposed as `ClientOptions.oauth2`. The client fetches and caches the bearer token itself, lazily, from `<base URL>/v1/oauth2/token`, sending the client credentials as `Authorization: Basic` on that token request and `Authorization: Bearer <access token>` on every business call.

```ts
const client = new PayPalServerSdkClient({
  oauth2: { clientId: process.env.PAYPAL_CLIENT_ID!, clientSecret: process.env.PAYPAL_CLIENT_SECRET! },
});
```

The credentials shape is `{ clientId, clientSecret, scope? }`. **All 40 operations require this credential; none is public.**

**`oauth2` is optional at the type level and that is a trap worth flagging on every sheet.** Omit it and nothing fails at construction — the request simply goes out with no credential and PayPal answers `401`. So a `401` on a call you believed was authenticated is usually an unset field rather than an SDK failure; verify the field is set rather than waiting for a `401` to tell you.

Three more behaviours the type does not show:

- The token request goes through **the same client** — same `timeout`, same `fetch`. A token cached until shortly before expiry, a response with no `expires_in` treated as never expiring, and concurrent callers sharing one in-flight fetch.
- **A refused token endpoint rejects with `AuthError`**, wrapping the underlying `ResponseError` as `cause` — so a bad client secret never looks like the business call failing, and `AuthError` is *not* a `ResponseError`.
- **A 401 invalidates, it does not retry.** On a 401 (401 only, not 403) the SDK clears the cached token so the *next* call re-acquires; the current request still rejects.

`oauth2Strategy` on `ClientOptions` substitutes the whole token request (`getToken(credentials, signal)`); the caching, expiry buffer and single-flight behaviour still apply. See `typescript-authentication` for the full picture.

## Resources

Resources are **memoized lazy getters** on the client (`client.<attr>`). Their classes are exported only for their merged namespaces — the per-operation request and error types — and for `instanceof`; their constructors take engine internals that are not exported, so reach a resource only through its getter.

| Attribute | Class | Ops | Operations |
| --- | --- | --- | --- |
| `client.orders` | `Orders` | 8 | `authorizeOrder` · `captureOrder` · `confirmOrder` · `createOrder` · `createOrderTracking` · `getOrder` · `patchOrder` · `updateOrderTracking` |
| `client.payments` | `Payments` | 7 | `captureAuthorizedPayment` · `getAuthorizedPayment` · `getCapturedPayment` · `getRefund` · `reauthorizePayment` · `refundCapturedPayment` · `voidPayment` |
| `client.vault` | `Vault` | 6 | `createPaymentToken` · `createSetupToken` · `deletePaymentToken` · `getPaymentToken` · `getSetupToken` · `listCustomerPaymentTokens` |
| `client.transactionSearch` | `TransactionSearch` | 2 | `searchBalances` · `searchTransactions` |
| `client.subscriptions` | `Subscriptions` | 17 | `activateBillingPlan` · `activateSubscription` · `cancelSubscription` · `captureSubscription` · `createBillingPlan` · `createSubscription` · `deactivateBillingPlan` · `getBillingPlan` · `getSubscription` · `listBillingPlans` · `listSubscriptionTransactions` · `listSubscriptions` · `patchBillingPlan` · `patchSubscription` · `reviseSubscription` · `suspendSubscription` · `updateBillingPlanPricingSchemes` |

Every operation has the same call shape — `op(request, options?)`, one **flat, channel-blind** request object first and `RequestOptions` (`{ signal }`) second — and returns `ApiPromise<T, E>`.

⚠ **The request type name is not uniformly `{Operation}Request`.** Several operations take `{Operation}RequestParams` instead (`Orders.ConfirmOrderRequestParams`, `Subscriptions.ActivateSubscriptionRequestParams`, `Subscriptions.CancelSubscriptionRequestParams`, `Subscriptions.CaptureSubscriptionRequestParams`, `Subscriptions.CreateSubscriptionRequestParams`). Take the name from the operation's **Signature** bullet on its map page; never construct it from the method name.

The SDK map's resource table carries the same counts and links to a page per resource (`map/operations/<resource>.md`) — go there for the operations themselves.

## SDK map — look up first, open the file second

The SDK ships a generated map, and `package.json`'s `files` list includes it, so **installing the package gives you the map** — no clone is needed. It sits at the package root, the directory holding `package.json` and the `src/` tree:

- **`sdk-map.md`** — the index: client construction with the full `ClientOptions` table, the *Not on this SDK* table, the two error families with `ApiResult` and `.asApiResult()`, wire serialization for every channel, **the full enum table with every member and its wire value**, servers and auth, runtime and packaging, and the link table into the operations pages.
- **`map/operations/<resource>.md`** — one page per resource, one `###` block per operation, with bullets in the fixed order **Server**, **Signature**, **Wire** (verb and route), **Auth**, **Request body**, **SDK-sent**, **Returns**, **Error**, **Error arms** — then a **Fields** table giving every request field its channel, wire name, type, required flag and default, and a **Type sources** table naming the declaring file and schema value of every type the operation mentions.

Locate the installed package before you rely on a lookup:

```bash
node -e "console.log(require.resolve('pay-pal-server-sdk/package.json'))"
```

Failing that it is at `node_modules/pay-pal-server-sdk/`. **If the package is not installed, there is no map and no source to read** — mark the fact `UNVERIFIED` and say what would settle it rather than answering from memory.

Every `Source` path on the map is relative to that package root, so `src/models/order.ts` opens as written from there — the package ships its `src/` tree, so the path resolves inside `node_modules/pay-pal-server-sdk/` exactly as the map writes it. An import specifier ending `.js` inside that source is the NodeNext spelling of the sibling `.ts` file.

**The map is the locator; the source files are the shapes.** Read the map first — signatures, routes, request fields with their channels and defaults, return types, error arms, enum values, and which file declares a type are all answered there without opening a single `.ts` file. Then open the one file the map names for what it deliberately does not carry: a model's members, whether each is required, optional or nullable. The map says so itself — *"Shapes live only in the source … Do not derive the path from the type name."*

**`sdk-map.md` carries the invariants every operation block assumes, so read it before any `map/operations/` page**; the pages are written to be read beside it. And **silence means the default**: the index states what holds for every operation — the `op(request, options?)` call shape, the flat channel-blind request object, the `ApiPromise<T, E>` return, the `default` server group, no pagination and no streaming — and a block departs from one only by saying so. Take the default and move on rather than opening the source to confirm it.

**The map carries shapes; what an operation *means* lives elsewhere.** When *what* to pass depends on meaning — which values a field accepts beyond its type, a rule that couples two fields, what a defaulted header actually selects — the map will not settle it. Read that operation's entry in `api-reference.md` at the package root, keyed by the same signature, *before* writing the sheet row, and record what you found. A value you already "know" for a field the map types as a plain `string` is a lookup, not a recall — the memory ban applies to it.

## Contract facts — the map first, then the source file

**Seven of these are map lookups — don't open a source file for them:** an operation's signature, its request fields with channel, wire name, required flag and default; its return type; its error subclass and the arms with the status each covers; the `ClientOptions` fields and their defaults; the environments, base URLs and auth wiring; **and every enum's members with their wire values**, which `sdk-map.md` tabulates in full.

The table below covers everything else, and the full body behind a map row. Paths are relative to `node_modules/pay-pal-server-sdk/`:

| Question | File |
| --- | --- |
| A model's members, required (`f: T`) vs optional (`f?: T`) vs required-nullable (`f: T \| null`) | `src/models/<file the Type sources table names>.ts` |
| A typed error body's members (`Error`, `SubscriptionError`, `DefaultError`, `SearchError`) | `src/models/error.ts`, `subscription-error.ts`, `default-error.ts`, `search-error.ts` |
| The operation method body and the request it builds | `src/resources/<resource>.ts` |
| The per-operation request and error types (merged namespace) | the `export namespace <Resource>` block at the foot of the same file |
| Client construction, resource getters | `src/client.ts` |
| `ClientOptions` fields and `DEFAULT_CLIENT_OPTIONS` | `src/client-options.ts` |
| Environments, base URLs, override merging | `src/servers.ts` |
| Auth scheme wiring, token endpoint, credential placement | `src/auth-schemes.ts`, `src/core/auth/` |
| The transport: timeout clamp, `fetch` resolution, 401 invalidation, 2xx-vs-error split | `src/core/raw-client.ts` |
| Error classes and `ErrorKind` | `src/core/errors.ts`, `src/core/response-error.ts` |
| `ApiPromise`, `ApiResult`, `.asApiResult()`, the `Symbol.species` behaviour | `src/core/api-promise.ts` |
| `RequestOptions` (it is `{ signal }` and nothing else) | `src/core/api-request.ts` |
| Schema decode/encode, `SchemaError`, `Encoded<T>` | `src/core/validation/` |
| Wire serialization per channel | `src/core/param-value.ts`, `url.ts`, `headers.ts`, `params.ts` |
| What an operation *means* — field semantics, coupling rules | `api-reference.md` at the package root |

**Read scoped.** Search for the one symbol and read the lines around it rather than whole files, and never copy a design comment's rationale onto a contract sheet — the sheet carries facts an implementer must obey, not the reasoning behind them.

Keep lookups cheap — the rules that keep a session's context small:

- Collect the contracts for **every** in-scope operation in **one** pass — signature, request fields with channels and defaults, required members, the error arms, enum values — into a short **contract sheet** in your plan, then implement from the sheet. Don't re-open a map page per field, and never re-look-up a fact the sheet already carries.
- Recurse into a model's members only where the task actually sets them — PayPal's order and subscription models nest deeply, and a full transitive expansion is hundreds of rows nobody needs.
- **Never grep, glob or `find` the package to *locate* a type** — the map is the locator, and it says so. Grep only *inside* the file its **Type sources** table names, for the symbol. A sweep for a cross-cutting *shape* is a different question and is fine: "every field typed `unknown`", "every required-nullable member" are things nothing indexes, and one targeted `grep -rn` over `src/models/` is the right tool — record what it found on the sheet.
- Trust the compiler over this page: if a name here ever fails to type-check, re-read the file the table above names and report the drift; never patch around it from memory.

## Integration workflow — load the companion skill at each step

Before you write the code for each step, load the named companion skill — even if you have already read the relevant file. Each step calls out the trap the signature hides (in *parens*). A typical integration reaches them in this order:

1. **Client construction & lifetime** — load **typescript-client-initialization** before you write `new PayPalServerSdkClient(...)`. (*The signature won't tell you:* every option is optional, so a client built with no arguments compiles and talks to sandbox with no credential; the client must be **long-lived and app-scoped**, never rebuilt per request, because the resource getters and the OAuth 2 token cache live on it; there is no `close()` or `dispose()` — it owns no pool, only a `fetch`; and when no `fetch` is reachable the **constructor** throws `SdkError`, not the first call.)
2. **Authentication** — load **typescript-authentication** before you set credentials. The one scheme is `oauth2`. (*The signature won't tell you:* the field is optional — omit it and every request goes out unauthenticated with no failure at construction; the token is fetched lazily and cached on the client; a **failed token fetch** raises `AuthError`, which is not a `ResponseError` and **bypasses `.asApiResult()` entirely**; and a 401 invalidates the cache without retrying the current call. Load secrets from the environment or a secret store, never hardcode.)
3. **Calling an endpoint** — load **typescript-calling-endpoints** before the first `client.<resource>.<operation>(...)` call. (*The signature won't tell you:* the request object is **flat and channel-blind** — a field named `body` *is* the whole request body and every other field is fanned out to path, query or header by the SDK, so nothing is nested by channel; **an omitted field that has a default is still sent, with that default** — `prefer` defaults to `"return=minimal"` on 11 operations and shapes the response you get back; **11 operations resolve to `undefined`**, so `.asApiResult()` is the only way to observe their status; and `.asApiResult()` must be called on the value the operation returned, because `ApiPromise` overrides `Symbol.species` and `.then()`/`.catch()` hand back a plain `Promise` with the method gone.)
4. **Models** — load **typescript-models** the moment a request/response member is not a plain string or number. (*The signature won't tell you:* models are plain `type`s built from object literals — no constructor, no builder; `f?: T` means omit the key, while `f: T | null` is **required and nullable** and `null` is a distinct value; enums are **open** (`const` companion plus a union admitting `(string & {})`), so the schema validates the base type only and an unknown server value round-trips instead of throwing — use `.values` to test membership yourself; and every type has a schema companion (`orderSchema`) usable in both directions.)
5. **Error handling** — load **typescript-error-handling** before you write any `try/catch`. (*The signature won't tell you:* there are **two disjoint families** — `ResponseError` and its 40 per-operation subclasses for an API error status, and the `PayPalServerSdkError` set (`ConnectionError`, `TimeoutError`, `AbortError`, `SdkError`, `SchemaError`, `AuthError`) for no usable response — and neither is `instanceof` the other, so a complete catch needs both arms; **arm tags are schema-derived, not statuses** (see the sheet checklist below); a malformed 2xx body rejects with `SchemaError`, not `ResponseError`, and `.asApiResult()` does not convert it; and a missing response field the schema permits is silently `undefined` rather than any error at all.)
6. **Configuration & resilience** — load **typescript-configuration-resilience** when you set the base URL, timeouts, proxies, TLS, or logging. (*The signature won't tell you:* **the SDK performs no retries at all** — a failed call rejects once, so retry/backoff is entirely yours to build or deliberately omit; **there is no logging and there are no hooks, middleware or interceptors** — `ClientOptions.fetch` is the single extension point for all of it; `timeout` is client-wide with **no per-request timeout**, and a non-finite or non-positive value is not "no timeout" but a fallback to the transport's own ceiling; and a `fetch` replacement that drops `init.signal` makes both the timeout and every `RequestOptions.signal` inert.)
7. **Testing** — load **typescript-testing** before you stub the SDK. (*The signature won't tell you:* the seam is **`ClientOptions.fetch`**, not the client class and not the resource classes — whose constructors take unexported engine internals, so they cannot be instantiated in a test; stub bodies in **wire shape** and let the SDK decode them; assert on the request the SDK actually built, headers included; and cover the failure kinds a `ResponseError`-only test misses, `SchemaError` above all.)

## What a contract sheet must carry for this SDK

Beyond the usual signatures and model members, a PayPal TypeScript sheet is incomplete without these, because each one is a decision the implementer cannot make correctly from the signature alone:

1. **Which host each deployment talks to.** There is one `ServerEnvironment` member (`Sandbox`) and the default base URL is `https://api-m.sandbox.paypal.com`. Live PayPal is a `serverOptions.default.sandbox.baseUrl` override under a key still named `sandbox` — so the sheet must say, per deployment, what that value is and where it is set. This is row one because it is the row whose absence sends live credentials to sandbox, or test traffic to live.
2. **The 11 operations that resolve to `undefined`** — `orders.patchOrder` · `orders.updateOrderTracking` · `subscriptions.activateBillingPlan` · `subscriptions.activateSubscription` · `subscriptions.cancelSubscription` · `subscriptions.deactivateBillingPlan` · `subscriptions.patchBillingPlan` · `subscriptions.patchSubscription` · `subscriptions.suspendSubscription` · `subscriptions.updateBillingPlanPricingSchemes` · `vault.deletePaymentToken`. `await` gives you nothing to inspect, so **`.asApiResult()` is the only way to observe their status and headers** — decide the mode at write time, not by retrofit.
3. **The exact request type name per operation**, taken from the **Signature** bullet — five operations take `{Operation}RequestParams`, not `{Operation}Request` (listed under *Resources*).
4. **Every request field with its channel, wire name and default**, because the request object is flat and channel-blind and the SDK fans fields out. Two PayPal-specific ones belong on every sheet row that has them:
   - **`prefer`** — a `header` field wired to `Prefer`, present on 11 operations with the default `"return=minimal"`. **An omitted field that has a default is still sent with that default**, so those calls ask PayPal for a minimal representation unless you pass `"return=representation"`. A sheet row whose code reads fields off the response must say which of the two it asked for.
   - **`payPalRequestId`** — a `header` field wired to `PayPal-Request-Id`, present on 12 operations and **optional**. This is the real idempotency key; the SDK injects nothing and `RequestOptions` is `{ signal }` only, so if the sheet does not name a value and where it comes from, the write has no idempotency at all.
5. **Required vs optional vs required-nullable** for every model member the task sets — `f: T` required, `f?: T` omit the key, `f: T | null` required and nullable. And that under `exactOptionalPropertyTypes` an absent optional is **omitted or spread**, never assigned `undefined`.
6. **The error arms for each operation in scope, with the status each covers — and the warning that arm tags are schema-derived, not status codes.** Every operation rejects with its own `ResponseError` subclass narrowed on `err.payload.kind`, and the tags are numbered per operation (`"error"`, `"error2"`, `"error3"`, …) in declaration order. **The same tag means different statuses on different operations, and the same status carries different tags** — `"error3"` is 422 on `orders.createOrder` but 404 on `payments.getRefund`; a 403 is `"error2"` on `orders.confirmOrder` but `"error3"` on `orders.authorizeOrder`. So a tag is only meaningful beside the arm table it came from, and a shared helper that switches on `kind` across operations is a bug. Four typed bodies exist, and all 40 operations are typed:
   1. `Error` — 21 operations (all of `orders`, `payments` and `vault`)
   2. `SubscriptionError` — 17 operations (all of `subscriptions`)
   3. `DefaultError` — 1 operation (`transactionSearch.searchBalances`)
   4. `SearchError` — 1 operation (`transactionSearch.searchTransactions`), whose only typed arm is the `[400–599]` catch-all
   Every operation also carries an always-present `"undeclared"` arm holding `rawBody: ArrayBuffer`, and most carry a wide `[400–599]` arm — for which **matcher precedence** matters: an exact numeric status is looked up across the whole table first, and only then does the first covering wildcard or range win. ⚠ `payments`' 500 arm (`"error500"`) is declared **with no body**, so narrowing to it gives you a status and nothing to read.
7. **That a malformed or drifted 2xx body rejects with `SchemaError`, not `ResponseError`, in both response modes** — `.asApiResult()` converts an HTTP error status, never a Family B failure. **And that no return type declares required members**, so a thin or truncated body decodes without complaint and the hole surfaces later. Any sheet row for a call whose result is used must name the members the implementer has to assert on.
8. **That the SDK performs no retries, no logging, no pagination and no streaming at all**, and that `ClientOptions.fetch` is the one seam where any of it can be added — so whatever the task needs there is yours to build or deliberately omit. Say which.
9. **That `Error` imported from this package is PayPal's error body and shadows the global `Error`** — every sheet that references a typed error arm should carry the alias it will be imported under.
10. A **REQUIRED READING** block naming the `typescript-*` companions that govern the steps, with inline `MUST load` pointers.
