---
name: maxio-getting-started
description: Identify and orient in the Maxio Advanced Billing (formerly Chargify) .NET SDK — its NuGet package id and root namespace, the namespace layout, US/EU environments and the Basic-auth pattern, and how the map points you to the exact source file when one is needed (maxio-debug clones it, at the pinned ref, only when the map falls short). Bundles a generated SDK map (sdk-map.md + map/) — a table of contents over every operation signature, error type, model, enum, and union — so you look facts up in the map and open exactly the source file you need instead of grepping the clone. This is the helper-facing reference layer, preloaded for the maxio-plan and maxio-debug subagents; the main agent routes Maxio work through integrate-maxio and works from its contract sheet instead of loading this skill or the map wholesale. It also routes you to the companion dotnet-* skills (client-setup, auth, calling endpoints, models, error handling, configuration/resilience, testing) and gates loading each at its step — load them even after you've read the SDK source, since the source shows signatures but not the usage gotchas these skills carry.
---

# Getting started with the Maxio Advanced Billing .NET SDK

> **Who this skill is for.** This is the helper-facing reference layer, preloaded for the
> `maxio-plan` and `maxio-debug` subagents — if you are one of them, it is yours to follow
> directly and fully. If you are the **main agent**, do not load this skill, the map pages,
> or the companion `dotnet-*` skills wholesale: route the task through `integrate-maxio`
> and implement from the contract sheet it produces (the router allows it one targeted
> map-page read when a fact is missing mid-implementation). The "load the companion skill" steps below address whoever
> is doing the grounding — in this plugin's flow, the helpers; the main agent receives
> those traps as one-line notes on the contract sheet. This skill never calls back into
> the router, so there is no loop.

This is the **SDK-specific** entry point. For general patterns that apply to any APIMatic-generated
.NET SDK (auth, calling endpoints, models, error handling, retries, testing), see the companion
API-agnostic skills: `dotnet-client-initialization`, `dotnet-authentication`, `dotnet-calling-endpoints`,
`dotnet-models`, `dotnet-error-handling`, `dotnet-configuration-resilience`, `dotnet-testing`.

**The SDK map and these companion skills are complementary — load both.** The map (generated from the SDK
source, which remains the ground truth) is authoritative for the SDK's *surface* (signatures, model shapes,
enums, which error type an operation throws); the companion skills are the *usage layer* on top — the
best-practice way to call each piece and the gotchas a signature can't show. Reading the map or source
doesn't remove the need to load the skill for that step, so at each step below, load the companion *and*
confirm names against the map.

> **Ground every signature, model, enum, and error type in the bundled SDK map** (`sdk-map.md` + `map/`,
> in this skill's directory) — it carries every operation signature, error type, enum value list, field
> list with JSON wire names, and union accessor by lookup, so most questions never touch the SDK source
> at all. When the map can't answer something — a full method/model body, or a map-sourced name that
> fails to compile — the SDK source must be consulted (`maxio-debug` clones it — see the *SDK source*
> section below) and the one file the map names opened; **never fill the gap from memory.** Do **not** decompile or
> reflect over the installed package, do **not** fetch GitHub files ad hoc, and do **not** grep or run
> other expensive searches over the clone.

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

The table above is **orientation, not a copy-paste recipe** — it gives you the names and facts (package id,
namespaces, the auth *pattern*, the environments), while the actual integration code comes from the companion
skills. Load each one as you reach its step (see **Integration workflow** below) and confirm its types
against the SDK map: the client construction and DI from `dotnet-client-initialization`, the exact
auth-credentials property name from `dotnet-authentication`, each call from `dotnet-calling-endpoints`, and so
on.

## Namespaces (using-directives)

The SDK splits its public types across **separate child namespaces**. C# does **not** import child
namespaces transitively, so `using MaxioAdvancedBilling.Models;` alone does **not** make enums, union
types, or error types visible — you get `CS0103`/`CS0246` ("name/type does not exist") on build. Add a
separate `using` for each kind of type you reference — the map lists each type's namespace, so take it from
the map row; only if the map is silent does `maxio-debug` open the file in the clone and copy the `namespace`
declaration at its top.

## Install — always via NuGet

Add the published NuGet package to your project. **Do not** add a project reference to the SDK's `.csproj`
or copy/clone its source into your solution — depend on the package only. (For *reading* the SDK source while
you code, clone it separately as a read-only reference — see the **SDK source** section below.)

```bash
dotnet add package AsadAli.AdvancedBilling.Sdk
```

> The NuGet **package id** (`AsadAli.AdvancedBilling.Sdk`) differs from the **root namespace** you import
> (`MaxioAdvancedBilling`): install by the package id, but write `using MaxioAdvancedBilling;` in code.
> Package: <https://www.nuget.org/packages/AsadAli.AdvancedBilling.Sdk/>. Runtime dependencies are pulled
> in transitively: `Polly`, `Microsoft.Extensions.Http`, `System.Net.Http.Json`,
> `System.Net.ServerSentEvents`.

## SDK map — look up first, open second, never grep

This skill bundles a generated table-of-contents for the SDK, right next to this file:

- **`sdk-map.md`** — the index: SDK identity (package id, namespace, version, source commit), the
  client-construction and error-handling models, the servers/auth wiring, and link tables into `map/`.
- **`map/operations/{Controller}.md`** — one page per controller (33 pages, 247 operations). Each operation
  row carries the HTTP verb/path, the exact C# signature with must-pass-explicitly params, the return type,
  the error case (typed `{Operation}Error` vs `RawError`) with its `TryGet…` accessors, and pagination.
  Each page's header names the source file it came from (e.g. `Source: Api/Customers.cs`).
- **`map/models/`** — record models (four alphabetical pages), `unions.md` (variant factories + `TryGet…`),
  and `enums.md` (full value lists).

**This map is how you traverse the SDK.** Do **not** grep, Glob, `find`, or otherwise scan the clone to
locate an operation, model, enum, union, or error type — that burns time and context on a 600+-file tree
whose entire surface is already indexed here. Instead:

1. Open `sdk-map.md` and follow the link table to the branch page you need (controller or model group).
2. Read the fact by lookup — most questions (signature, error accessors, enum values, pagination) end here
   without touching the clone at all.
3. Only when you need a **full method or model body** the map doesn't carry, take the file path the map
   names (e.g. `Api/Customers.cs`, `Errors/CreateCustomerError.cs`, `Models/CreateCustomerRequest.cs`) and
   **open that one file directly in the clone**.

Keep lookups cheap — the rules that keep a session's context small:

- Collect the contracts for **every** in-scope operation in **one** map pass — signature, required fields
  with wire names, error accessors, enum values — into a short **contract sheet** in your plan or working
  notes, then implement from the sheet. Don't re-open the map per field, and never re-look-up a fact the
  sheet already carries.
- When you do open a source file, read it **scoped**: the Read tool with an offset/limit, or the Grep tool
  on the one symbol — never dump a whole file into the conversation with `cat`/`sed`.
- Never open the SDK's `api-reference.md` — the map supersedes it.

Staleness check: `sdk-map.md` records the SDK version and source commit it was generated from. If a name
from the map fails to compile, trust the compiler and re-read the source file the map's row names.

## SDK source — clone only on a map-side issue; don't reflect or fetch files

The clone is the ground truth the map was generated from, but it is a **last resort, not a step** — most
integrations never open it. Clone only when the map has actually failed you: a map-sourced name fails to
compile, ambiguity remains after the map lookup, or you need a full method/model body the map doesn't
carry. (In this plugin's flow cloning is **`maxio-debug`'s** move; `maxio-plan` never clones — it flags the
source-only fact for `maxio-debug`. Nobody clones "just in case".) The clone and the session file that
records its path both live in the **system temp directory** (`<temp>/maxio-sdk-src/`), never in the project
repo, so the clone stays invisible to the main agent — navigated via the SDK map above, a read-only
reference, **not** a build dependency (never add a project reference to it).

**Cloning is `maxio-debug`'s job, and only on a real map-side issue.** If you are `maxio-plan`, never clone:
flag the source-only fact for `maxio-debug`. If you are `maxio-debug`, first check the shared session file
`<temp>/maxio-sdk-src/.maxio-session.md` for a clone already recorded this session and reuse its path;
otherwise clone once — shallow, **pinned to `v1.0.2`** (the ref the map was generated from) — into a fresh
timestamped folder, then record the path in that session file (create it if absent) so every later debug
spawn reuses it:

```bash
# Linux/macOS:
dir="${TMPDIR:-/tmp}/maxio-sdk-src/$(date +%Y%m%d-%H%M%S)"
git clone --depth 1 --branch v1.0.2 https://github.com/asadali214/advanced-billing-sample-sdk "$dir"
# Record "$dir" in <temp>/maxio-sdk-src/.maxio-session.md — it is your clone path for the session.
```

```powershell
# Windows (PowerShell):
$dir = "$env:TEMP\maxio-sdk-src\$(Get-Date -Format yyyyMMdd-HHmmss)"
git clone --depth 1 --branch v1.0.2 https://github.com/asadali214/advanced-billing-sample-sdk $dir
# Record $dir in $env:TEMP\maxio-sdk-src\.maxio-session.md — it is your clone path for the session.
```

The session file lives in `<temp>/maxio-sdk-src/`, **never** in the project repo, and the clone path never
goes into `maxio-plan.md` — the main agent must never see the clone or its path.

Then confirm the SDK shape **only** from that local clone — not by either of these:

- **Don't decompile or run reflection over the installed package.** A compiled assembly drops what the source
  carries and the other skills rely on: the XML `<exception>`/parameter doc-comments, the exact parameter
  names and order, and each method's internal `new Param("snake_case", …)` request-builder list.
- **Don't fetch GitHub files one at a time** as your way in — `…/blob/…` pages return HTML (not source) and
  guessed paths fail, which is exactly how ad-hoc fetching breaks. Clone once and read locally instead. Only
  if you truly cannot clone (`git` is unavailable) fetch a **raw** URL of the form
  `https://raw.githubusercontent.com/asadali214/advanced-billing-sample-sdk/v1.0.2/…` (never a `…/blob/…`
  page) — e.g. `…/v1.0.2/Api/Customers.cs` or `…/v1.0.2/Models/CreateCustomerRequest.cs`.

Layout — where the SDK map's file references resolve (open these directly; don't scan for them):

- `Api/` — one file per controller/group; **this is where the operation methods and their signatures live**
  (each carries XML-doc comments for the params, the endpoint path, and the thrown error type). The map's
  per-controller pages name the exact file (e.g. `Api/Customers.cs`).
- `Models/` (+ `Models/Enums/`, `Models/AnyOf/`, `Models/OneOf/`) — request/response records, enums, unions.
- `Errors/` — per-operation `{Operation}Error` types (only Case-A operations have one; the map's rows say
  which case each operation is).
- `Core/` — HTTP infrastructure (`SdkException<T>`, `RawError`, `ApiResult<T>`, auth, retries).
- `Servers/`, `MaxioAdvancedBillingClient.cs`, `ServiceCollectionExtensions.cs` — environments, the client, DI.

The clone above is already pinned to `v1.0.2` (the ref the map was generated from), so it matches the map.
If you ever need a different version, check out its tag (`git -C "$dir" checkout vX.Y.Z`).

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
   the base URL, pagination, or logging. (*The signature won't tell you:* retries cover idempotent verbs only —
   anything not in `GET/HEAD/PUT/OPTIONS` (`POST`, `PATCH`, `DELETE`) is not retried — `Timeout` is
   per-attempt not total, and there's no built-in logging hook.)
7. **Testing** — load **dotnet-testing** before you stub the SDK. (*The signature won't tell you:* the
   `HttpClient` constructor argument is the test seam; match the project's existing framework and assertion
   style.)
