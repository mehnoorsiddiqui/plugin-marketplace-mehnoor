---
name: python-integrate-maxio
description: MANDATORY FIRST STEP for Maxio Advanced Billing (Chargify) Python SDK work in a Python project — load this BEFORE you write any code; Python SDK ONLY, never load it for any other language. Applies when asked to integrate Maxio, Advanced Billing or Chargify in Python — subscriptions, recurring billing, invoices, proforma invoices, coupons, components, price points, customers, payment profiles, product families, offers, webhooks, events-based billing, reason codes, sales commissions, transaction or insight reporting — or when a Maxio Python SDK call errors or behaves unexpectedly. Knowing the SDK exists is NOT a substitute for loading this, because it carries five binding gates stated NOWHERE else and not inferable from the package — (1) load `python-integration-planning` before any plan and `python-getting-started` before any lookup, (2) the exact plan-file path and the no-project-file-edits window until a contract sheet with no open lookups exists there and you have read it, (3) the mandatory load of every python-* companion the sheet names, (4) sync-vs-async and the server's URL template variables decided once before the first call, and (5) the memory ban, where every signature, wire alias, error arm and enum member comes from the SDK map and never from recall or runtime introspection.
---

# Maxio Advanced Billing Python SDK — integration workflow

## Load first, before anything else

**`python-integration-planning` is unconditional for every implementation task.** It asks the seven
questions a plan must answer — and rows **D (inbound state)** and **F (change safety)** are the two that
get skipped, because nothing in the code looks wrong without them. Maxio makes row D concrete: it sends
**webhooks**, and `client.webhooks` carries 6 operations.

The contract sheet below answers *what shape the SDK is*. That skill answers *what must be true before
this ships*. They are different artifacts and you need both.

Then, as the work reaches them: `python-getting-started` → `python-client-initialization` →
`python-authentication` → `python-calling-endpoints` → `python-models` → `python-error-handling` →
`python-configuration-resilience` → `python-inbound-state` → `python-sdk-drift` → `python-testing`.

## Your lookup layer

**`python-getting-started`** is the entry point for every SDK need, and it bundles the generated **SDK
map** — `sdk-map.md` plus 34 controller pages under `map/operations/`. Load it first, always.

- **The map is the locator; the installed package is the ground truth.** Confirm the package is importable
  before you rely on a lookup:
  `python -c "import maxio_advanced_billing, pathlib; print(pathlib.Path(maxio_advanced_billing.__file__).parent)"`.
  **If it is not installed there is no source to read** — mark the fact `UNVERIFIED`, say what would settle
  it, and do not fill the hole from memory.
- **Read scoped.** `grep -n` for the symbol *inside* the one module the map names. Never a whole file, and
  never copy a docstring's design rationale onto a contract sheet.
- **Write a contract sheet with no open lookups** before you implement. `python-getting-started` ends with
  the checklist of rows a Maxio sheet is incomplete without — collect every in-scope operation in ONE pass
  rather than re-opening a page per member.

**Scope guard:** the APIMatic-generated **Maxio Advanced Billing Python SDK** (import root
`maxio_advanced_billing`, distribution `maxio-advanced-billing`) in **Python projects only**. Unrelated
API, or any language other than Python — do nothing; this skill does not apply.

## Workflow

**If the user opens with a reported SDK error or unexpected Maxio behaviour** (not new feature work), skip
the plan-first flow: load `python-getting-started` and `python-error-handling`, look the failing symbol up
on the map page for its controller, and fix from what you find. Otherwise, for implementation work:

### Step 1 — Plan first (always, for any implementation work)

Your FIRST action is to load `python-integration-planning`, then `python-getting-started`, and work
through the user's full request (all features in scope — one plan covers the whole implementation). Then
write the plan and its contract sheet to `<project repo root>/maxio-plan.md` — that absolute path, not a
location you pick later.

**Do the read-only prerequisites in the same pass — they need no SDK knowledge and touch no project file:**

- the repo survey (conventions, layering, and **whether the project is sync or `async`**) — capture each
  convention as *pattern + the ONE exemplar file path to imitate*, not inline code snippets;
- **establish the toolchain**: which environment manager the repo uses (`uv`, `poetry`, `pip` + venv,
  `pdm`), whether `maxio-advanced-billing` is already a dependency and at what version, and the exact
  commands that run the project's tests and type checker;
- a baseline run of the project's checks on the UNTOUCHED tree, so later failures are attributable;
- credentials and environment verification — **including which `environment` you target and what `{site}`
  or `{connector}` resolves to.** Leaving the template variable unset silently points every call at
  `https://subdomain.chargify.com`;
- setting up your task tracking.

Never use the planning phase to get a "head start" on implementation: **creating or editing ANY project
file before the gate below is a defect**, no matter how obvious the code seems.

**HARD GATE — no project-file creation or edits until:** `maxio-plan.md` EXISTS at the repo root, its
contract sheet has **no open lookups**, and you have read it. The gate bars coding "meanwhile"; it does
not bar the read-only prerequisite work above.

### Step 1c — Required reading (before you write any code)

End the contract sheet with a **REQUIRED READING** block whose rows carry inline `MUST load <skill>`
pointers. **Load every `python-*` skill the sheet names, now, before you start implementing** — not lazily
at the step that needs it. The sheet deliberately does *not* carry the how-to: it names the hazard and
hands you the skill that resolves it, so an unloaded pointer is a gap in what you know.

If the sheet names none, load `python-error-handling` anyway — every integration writes an error boundary.

Then check the plan's **Assumptions & Blockers**: a blocker or major assumption goes to the user in plain
language before you implement; minor assumptions only, proceed.

### Step 2 — Implement from the contract sheet

1. **Read `maxio-plan.md` once.** Treat its contracts as authoritative — do not re-derive or
   "double-check" them from memory. When a lookup revises a row, update the file so sheet and code never
   disagree.
2. **Decide sync vs async once, from the host application, before the first call.** This SDK ships two
   complete client classes (`MaxioAdvancedBillingClient` / `AsyncMaxioAdvancedBillingClient`) with no
   bridge between them: a sync client in an `async def` blocks the event loop, an async client in sync code
   is a coroutine nobody awaits. Getting this wrong is not a local fix later; it is every call site. It also
   changes the teardown obligation — `close()` vs `await aclose()`.
3. **Resolve the server before the first call.** Each operation names its own server (`production`, `ebb`,
   `oauth`) on its map block, and the base URLs carry `{site}` / `{connector}` template variables whose
   defaults are placeholders. Pass `server_config` for every server the work touches — EBB ingestion keeps
   its own host even under the gateway environment.
4. **Pick the response mode per call, deliberately.** Each operation exists twice: the plain call raises
   `ApiError`, its `.with_raw_response` peer returns `ApiResult` instead. **29 operations return `None`** —
   for those the raw peer is the only way to observe the status code, so choose at write time.
5. Implement sequentially, following the repo's own conventions and layering. Take every contract *fact*
   (signatures, wire aliases, error arms, enum members) from the sheet or a fresh lookup — never re-derive
   one from a companion skill.
6. After every change run the project's **type checker** as well as its tests. The package ships `py.typed`
   and is generated under strict typing, so a type error against it is a real contract violation — the
   closest thing Python gives you to a compile step. Treat a clean type check as a gate you do not skip.
7. **Any error involving an SDK type or member** — an `AttributeError`/`TypeError` on a
   `maxio_advanced_billing.*` object, a `pydantic.ValidationError` you cannot place, a type error naming an
   SDK type, an unexpected `ApiError` — go back to the map block for that operation and read the one module
   its **Type sources** table names. Do not attempt more than one self-fix of an SDK-name error before doing
   that lookup: rewriting from the same knowledge that produced the error is guessing. Remember a **decode
   failure raises `ValidationError`/`ValueError`, not `ApiError`, and bypasses both response modes**, and
   that `httpx` transport exceptions arrive unwrapped.
8. Run the project's tests and verify end to end the way the task demands. **The SDK performs no retries at
   all and ships no paginator** — anything the task needs there is yours to build or deliberately omit; say
   which you did.

### Step 3 — Answering pure questions

A standalone Maxio question with no code change: look it up on the map (and the module its **Type sources**
table names), then give the grounded answer. Never answer from memory, even for "easy" questions.

## Anti-patterns — never do these

- **Skipping `python-integration-planning`** because the task looks small. Rows B, D and F cannot be
  retrofitted cheaply: B changes the call site, D changes the architecture, F changes the test suite.
- **Writing a Maxio/SDK fact from memory** — every signature, field name, wire alias, enum value and error
  arm comes from the map or the module it names. And never write a call from memory "to fix later".
- **Locating something by `grep`, `glob` or `find` over the SDK tree.** The map is the locator: open its
  index, follow the link. A tree scan pulls un-grounded source into context and is slower than the lookup
  it replaces.
- **Leaving `{site}` or `{connector}` at its default.** Nothing fails at construction; the calls simply go
  to a host that is not yours.
- **Assuming credentials are validated.** Both `basic_auth` and `bearer_auth` default to `None`, which
  installs `no_auth` — requests go out unauthenticated and return `401`, with no error at construction.
- **Re-guessing a failing symbol.** One rewrite from memory after an error is how the error happened; the
  second is a choice.
- **Introspecting the SDK at runtime to discover its shape** — `dir()`, `model_fields`,
  `inspect.signature`, a REPL poke. It answers what exists, never what is *supported*, and invites private
  attributes into your code. Read the module the map names.
- **Web-searching Maxio or Chargify for an implementation detail.** The public docs describe the REST API,
  not this SDK's generated surface, and the two disagree on names.
- **Creating or editing project files before the HARD GATE** in Step 1 — plan and contract sheet first,
  code second.
