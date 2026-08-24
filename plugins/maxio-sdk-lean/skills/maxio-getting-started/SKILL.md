---
name: maxio-getting-started
description: Maxio Advanced Billing (formerly Chargify) .NET SDK identity and lookup layer for the maxio-sdk helper agent — package id, root namespace, environments, auth pattern, and the SDK map of every operation signature, model, enum, union and error type. The helper agent loads this to answer contract questions; other agents work from the contract sheet it produces.
---

# Getting started with the Maxio Advanced Billing .NET SDK

> **Who this skill is for.** This is the **map layer**, preloaded for the `maxio-sdk` helper
> agent — if you are it, this skill is yours to follow directly and fully. You are the only one
> who clones the SDK and reads its map, and both stay with you: an implementer works from the
> contract sheet you produce, and asks you for any fact the sheet is missing. The clone and its
> path never leave this agent. This skill never calls back into the router, so there is no loop.

## Your three sources

Every fact you emit comes from one of these. Nothing comes from memory.

| Source | What it gives you | Where |
| --- | --- | --- |
| **SDK map** | The SDK's **structure and surface**: each operation's exact signature (parameter order, types, the nullables you must pass explicitly), return type, error case with its typed `TryGet…` accessors, query-param wire names and pagination; each record's fields with JSON wire names, required flags and nullability; full enum value lists; union factories and accessors; client, auth and server wiring. It also names the source file behind every page. | `<clone>/sdk-map.md` + `<clone>/map/` |
| **SDK source** | The code the map is generated from — what settles anything the map leaves **ambiguous**: a full method or model body, the two per-operation facts in `Api/{Controller}.cs` (below), or a map name that fails to compile. | the same clone |
| **`dotnet-*` skills** | **How to use** the SDK: the best-practice pattern for each integration step, plus the defaults, worked examples and hazards a signature can't show. API-agnostic, so they name no Maxio types. | this plugin |

Work in that order — look the fact up in the map, open the one source file it names when the map is
ambiguous, and load the companion skill for each step you reach (see **Integration workflow**). Knowing a
name does not tell you how to use it correctly, so the map never removes the need for the skill, nor the
skill for the map.

## SDK identity

| | |
| --- | --- |
| API | Maxio Advanced Billing (formerly Chargify) |
| NuGet package | `AsadAli.AdvancedBilling.Sdk` |
| Root namespace | `MaxioAdvancedBilling` (the `using` namespace — note it differs from the package id) |
| Client class | `MaxioAdvancedBillingClient` |
| Options class | `MaxioAdvancedBillingClientOptions` |
| Auth | HTTP **Basic** — username = API key, password = literal `"x"` |
| Environments | `ServerEnvironment.Us` (default) → `https://{site}.chargify.com`; `ServerEnvironment.Eu` → `https://{site}.ebilling.maxio.com` |
| Target framework | `netstandard2.0` (works on .NET Framework 4.6.1+, .NET Core 2.0+, .NET 5–10+) |

This table is **orientation, not a copy-paste recipe**: it gives you the names, the auth *pattern* and the
environments. The integration code itself comes from the companion skills.

## Namespaces (using-directives)

The SDK splits its public types across **separate child namespaces**. C# does **not** import child
namespaces transitively, so `using MaxioAdvancedBilling.Models;` alone does **not** make enums, union
types, or error types visible — you get `CS0103`/`CS0246` ("name/type does not exist") on build. Add a
separate `using` for each kind of type you reference — the map lists each type's namespace, so take it from
the map row; where a row leaves it unclear, open that type's file in the clone and copy the `namespace`
declaration at its top.

## Install — always via NuGet

Add the published NuGet package to your project. **Do not** add a project reference to the SDK's `.csproj`
or copy/clone its source into your solution — depend on the package only. (For *reading* the SDK map and the
source it describes while you code, clone the repo separately as a read-only reference — see the
**SDK source & map** section below. That clone is never the build dependency.)

```bash
dotnet add package AsadAli.AdvancedBilling.Sdk
```

> The NuGet **package id** (`AsadAli.AdvancedBilling.Sdk`) differs from the **root namespace** you import
> (`MaxioAdvancedBilling`): install by the package id, but write `using MaxioAdvancedBilling;` in code.
> Package: <https://www.nuget.org/packages/AsadAli.AdvancedBilling.Sdk/>. Runtime dependencies are pulled
> in transitively: `Polly`, `Microsoft.Extensions.Http`, `System.Net.Http.Json`,
> `System.Net.ServerSentEvents`.

## SDK map — the pages, and how to read them

The map sits at the root of the clone:

- **`sdk-map.md`** — the index: SDK identity (package id, namespace, version, source commit), the
  client-construction and error-handling models, the servers/auth wiring, the SDK-wide defaults the
  operation rows rely on, and link tables into `map/`.
- **`map/operations/{Controller}.md`** — one page per controller (33 pages, 247 operations). Each row
  carries the exact C# signature with must-pass-explicitly params and defaults, the query-param wire names,
  the return type, and the error case (typed `{Operation}Error` vs `RawError`) — Case A rows list their
  `TryGet…` accessors with the status each maps to. Each page header names the source file it came from
  (e.g. `Source: Api/Customers.cs`), which resolves in this same clone.
- **`map/models/`** — record models (four alphabetical pages), `unions.md` (variant factories + `TryGet…`),
  and `enums.md` (full value lists).

**A row states what is specific to its operation.** The SDK-wide defaults — throw-only, no pagination, the
four fixed Case B accessors, the `Production` server group — are stated once in `sdk-map.md` and hold for
every operation, and a row appears only where its operation departs from one. So a row that says nothing
about pagination *tells you* that operation has none: record the default in your sheet and move on.

**`Api/{Controller}.cs` holds two further facts about each operation**, one scoped read away in this clone:

- **the HTTP verb and route** — you call the C# method, not the URL, so reach for the route when something
  wire-level needs it (a raw request, mock wiring, reading a provider log) and read it there rather than
  inferring it from the method name.
- **the endpoint's behavioural prose** — the XML `<remarks>` on the method. This is what settles an
  operation whose *semantics* decide what you must pass: a parameter that changes server-side behaviour, an
  ordering or exclusivity rule between fields.

**How to read the map — the guard, non-negotiable:**

1. Open `sdk-map.md` and follow the link table to the page you need (controller or model group).
2. Read the fact by lookup. Most questions — signature, error accessors, enum values, pagination — end here.
3. When the map leaves a fact ambiguous, take the file path the map's row names (e.g. `Api/Customers.cs`,
   `Errors/CreateCustomerError.cs`, `Models/CreateCustomerRequest.cs`) and open **that one file**, **scoped**:
   the Read tool with an offset/limit, or the Grep tool on the one symbol. Never dump a whole file with
   `cat`/`sed`.

**Grepping, globbing, or `find`-ing over the clone to *locate* something is a defect.** The map is the
locator, and it indexes the entire surface of a 600+-file tree. You go map row → the named file — never
search → file.

Two more rules that keep a session's context small:

- Collect the contracts for **every** in-scope operation in **one** map pass — signature, required fields
  with wire names, error accessors, enum values — into a short **contract sheet** in your plan or working
  notes, then implement from the sheet. Don't re-open the map per field, and never re-look-up a fact the
  sheet already carries.
- **Don't open the SDK's `api-reference.md`.** It is the SDK's human-facing usage reference — per-operation
  code samples and prose — and it is one large file with no table of contents. Your surface lookups go to
  the map; your usage guidance comes from the `dotnet-*` skills.

Staleness check: the map ships with the source at the ref you cloned, so it matches that source **by
construction** — there is no separate map version that can drift out of step. If a name from the map fails
to compile, trust the compiler and re-read the source file the map's row names, in the same clone.

## SDK source & map — clone first, then map-first

The map is generated alongside the SDK and shipped at the root of its repo, so one clone gets you both the
map and the source it describes, always in lockstep. **Cloning is step 0** — it is what you read the map
from. The clone lives in the **system temp directory** (`<temp>/maxio-sdk-src/`), never in the project repo,
so it stays invisible to the main agent — navigated via the SDK map above, a read-only reference, **not** a
build dependency (never add a project reference to it).

**Clone once per session, then reuse that folder.** If a `<temp>/maxio-sdk-src/<timestamp>/` folder from
this session already exists, reuse the newest one rather than cloning again; otherwise clone shallow into a
fresh timestamped folder:

```bash
# Linux/macOS:
dir="${TMPDIR:-/tmp}/maxio-sdk-src/$(date +%Y%m%d-%H%M%S)"
git clone --depth 1 --branch docs/sdk-map https://github.com/mohammadali2549/advanced-billing-sample-sdk "$dir"
# Reuse "$dir" for the rest of your session (it is your clone path).
```

```powershell
# Windows (PowerShell):
$dir = "$env:TEMP\maxio-sdk-src\$(Get-Date -Format yyyyMMdd-HHmmss)"
git clone --depth 1 --branch docs/sdk-map https://github.com/mohammadali2549/advanced-billing-sample-sdk $dir
# Reuse $dir for the rest of your session (it is your clone path).
```

> **Clone source.** Clone the SDK **fork/branch that carries the map**
> (`mohammadali2549/advanced-billing-sample-sdk` @ `docs/sdk-map`) while the map is in review upstream; once
> it merges into the canonical SDK, clone `asadali214/advanced-billing-sample-sdk` at the released tag.
> Either way the map and the source in that clone are the same version by construction. The SDK **package**
> you install is unaffected by this: always `dotnet add package AsadAli.AdvancedBilling.Sdk` (the clone is
> for reading only), and this branch adds documentation only — the C# source in it is the `v1.0.2` code the
> package ships.

The clone lives in `<temp>/maxio-sdk-src/`, **never** in the project repo, and its path never goes into
`maxio-plan.md` or into your replies — the main agent must never see the clone or its path.

Read the map, and (only when the guard above permits) the source, **from that local clone** — not by either
of these:

- **Don't decompile or run reflection over the installed package.** A compiled assembly drops what the source
  carries and the other skills rely on: the XML `<exception>`/parameter doc-comments, the exact parameter
  names and order, and each method's internal `new Param("snake_case", …)` request-builder list.
- **Don't fetch GitHub files one at a time** as your way in — `…/blob/…` pages return HTML (not source) and
  guessed paths fail, which is exactly how ad-hoc fetching breaks. Clone once and read locally instead. Only
  if you truly cannot clone (`git` is unavailable) fetch a **raw** URL of the form
  `https://raw.githubusercontent.com/mohammadali2549/advanced-billing-sample-sdk/docs/sdk-map/…` (never a
  `…/blob/…` page) — e.g. `…/docs/sdk-map/sdk-map.md`, `…/docs/sdk-map/map/operations/Customers.md`, or
  `…/docs/sdk-map/Api/Customers.cs`.

Layout — where the map and its file references resolve in the clone (open these directly; don't scan for
them):

- `sdk-map.md` + `map/` — **the SDK map** (index + operations/models pages), at the repo root. Start here.
- `Api/` — one file per controller/group; **this is where the operation methods and their signatures live**
  (each carries XML-doc comments for the params, the endpoint path, the XML `<remarks>` describing the
  endpoint's behaviour, and the thrown error type). The map's per-controller pages name the exact file (e.g.
  `Api/Customers.cs`). **This file is the sanctioned source for the two things the map's rows omit by
  design** — the HTTP verb/route, and the behavioural prose that decides what you must pass.
- `Models/` (+ `Models/Enums/`, `Models/AnyOf/`, `Models/OneOf/`) — request/response records, enums, unions.
- `Errors/` — per-operation `{Operation}Error` types (only Case-A operations have one; the map's rows say
  which case each operation is).
- `Core/` — HTTP infrastructure (`SdkException<T>`, `RawError`, `ApiResult<T>`, auth, retries).
- `Servers/`, `MaxioAdvancedBillingClient.cs`, `ServiceCollectionExtensions.cs` — environments, the client, DI.

The map in your clone matches the source in the same clone by construction — there is no separate version to
reconcile. If you ever need a different SDK version, check out its ref (`git -C "$dir" checkout <ref>`) and
the map at that ref moves with it.

**Leave the clone in place — don't delete it.** It's a read-only reference with nothing of yours in it, and
keeping it is what lets every later step in this session reuse it instead of cloning again. The OS reaps the
temp directory on its own; a future session simply makes its own timestamped clone.

## Integration workflow — load the companion skill at each step

Before you write the code for each step, load the named companion skill — even if you've already read the
relevant source. Each step calls out the trap the signature hides (in *parens*). A typical integration reaches
them in this order:

1. **Client & DI setup** — load **dotnet-client-initialization** before you write
   `new MaxioAdvancedBillingClient(...)`, build its options, or DI-register via
   `AddMaxioAdvancedBillingClient`. (*The signature won't tell you:* the `HttpClient`/handler
   pipeline must be long-lived and reused via `IHttpClientFactory`, not rebuilt per request; the SDK client
   wrapper over it may be transient.)
2. **Authentication** — load **dotnet-authentication** before you set credentials. Maxio is HTTP Basic
   (username = API key, password = `"x"`). (*The signature won't tell you:* set credentials before
   constructing the client or in the DI callback, and load the key from configuration rather than hardcoding.)
3. **Calling an endpoint / building a request body** — load **dotnet-calling-endpoints** before the first
   `client.{ApiGroup}.{Operation}(...)` call. (*The signature won't tell you:* call list/search ops with
   named arguments — many optional params have no C# default and mis-bind in a positional call.)
4. **Models** — load **dotnet-models** the moment a request/response field isn't a plain string or number.
   (*The signature won't tell you:* unions are built with factory methods and read via `TryGet…` (no `new`),
   enums are `StringEnum<T>` not C# enums, and unmodeled JSON fields are dropped on deserialize.)
5. **Error handling** — load **dotnet-error-handling** before you write any `try/catch`. (*The signature won't
   tell you:* **many — not all —** read/list/find/delete ops are Case B (`SdkException<RawError>`, no typed
   accessors) while others are Case A typed `{Operation}Error`s — confirm each operation's case in its map
   row; and `TryGetRawError` is not a catch-all on the typed errors. This SDK generates **no**
   `{Operation}Result`/`ApiResult` no-throw variants — every operation is throw-only, so ignore the
   Result-style sections of the companion skills and always wrap the throwing call.)
6. **Configuration & resilience** — load **dotnet-configuration-resilience** when you tune retries, timeouts,
   the base URL, pagination, or logging. (*The signature won't tell you:* `HttpMethodsToRetry` gates only the
   **status** trigger, so a `503` on a `POST` is not resent — but a **transport failure**
   (`HttpRequestException`) is retried on **every** verb, `POST` included, so a non-idempotent write can
   execute more than once and no setting disables that (`MaxRetries = 0` is rejected at construction;
   the floor is 1). `Timeout` is per-attempt not total, and there's no built-in logging hook.)
7. **Testing** — load **dotnet-testing** before you stub the SDK. (*The signature won't tell you:* the
   `HttpClient` constructor argument is the test seam; match the project's existing framework and assertion
   style.)
