---
name: python-integrate-paypal
description: MANDATORY FIRST STEP for PayPal Python SDK work in a Python project — load this BEFORE you write any code; Python SDK ONLY, never load it for any other language (the C#/.NET SDK has its own entry point, dotnet-integrate-paypal). Applies when asked to integrate PayPal in Python — take a payment at checkout, capture, refund, save a card, subscriptions, billing plans, vaulted payment methods, transaction search — or when a PayPal Python SDK call errors or behaves unexpectedly. Knowing the SDK exists is NOT a substitute for loading this, because it carries four binding gates stated NOWHERE else — (1) load `python-getting-started` and write a contract sheet with no open lookups before any code, (2) the exact plan-file path and the no-project-file-edits window until it exists and you have read it, (3) the mandatory load of every python-* companion skill the contract sheet names, and (4) the memory ban, where every signature, wire alias, error union and enum member comes from a lookup and never from recall.
---

# PayPal Python SDK — Integration workflow

You do the SDK lookups yourself. `python-getting-started` is your **lookup layer** — load it and
ground every contract fact in it (and in the source modules its map names) before writing code. The
`python-*` companion skills are a different thing: they are API-agnostic *usage* guidance, and
Step 1c below makes loading them mandatory.

## Your lookup layer

- **`python-getting-started`** is the entry point for every SDK need. It carries the SDK's identity
  (distribution vs import root, version, Python floor), the client classes and what the root package
  exports, environments and the `base_url` knob, the auth pattern, and a **module map** that names
  the one file that owns each kind of fact. Load it first, always.
- **Read scoped.** Those modules carry long design docstrings. `grep -n` for the symbol and read the
  surrounding lines rather than whole files, and never copy a docstring's design rationale onto a
  contract sheet — the sheet carries facts an implementer must obey, not the reasoning behind them.
- **Write a contract sheet with no open lookups** before you implement: exact signatures,
  keyword-only boundaries, wire aliases, required-vs-`UNSET` members, the `ApiError.error` union per
  operation, and enum members for the operations in scope. `python-getting-started` ends with the
  nine rows a Python sheet is incomplete without — treat that list as the checklist for your own
  sheet.

**Scope guard:** the APIMatic-generated PayPal **Python SDK** (import root `pay_pal_server_sdk`,
distribution `pay-pal-server-sdk`) in **Python projects only**. Unrelated API, or any language other
than Python — do nothing; this skill does not apply. For C#/.NET, the sibling entry point is
`dotnet-integrate-paypal` with its `dotnet-*` companions.

## Workflow

**If the user opens with a reported SDK error or unexpected PayPal behaviour** (not new feature
work), skip the plan-first flow: load `python-getting-started` and `python-error-handling`, look the
failing symbol up in the source module the map names, and fix from what you find. Otherwise, for
implementation work:

### Step 1 — Plan first (always, for any implementation work)

Your FIRST action is to load `python-getting-started` and work through the user's full request (all
features in scope — one plan covers the whole implementation). Then write the plan and its contract
sheet to an absolute path at the project repo root: `<project repo root>/paypal-plan.md`.

**Do the read-only prerequisites too — they need no SDK knowledge and touch no project file:**

- the repo survey (read-only exploration of conventions, layering, and whether the project is
  sync or `async`) — capture each convention as *pattern + the ONE exemplar file path to imitate*,
  NOT inline code snippets: you will read the exemplar at edit time anyway (edits need the file's
  exact current text), so a snippet dump gets paid for twice;
- **establish the toolchain before you need it**: which environment manager the repo uses (`uv`,
  `poetry`, `pip` + venv, `pdm`), whether the package is already a dependency and at what version,
  and the exact command that runs the project's tests and type checker. Getting this wrong later
  costs a broken install mid-implementation;
- a baseline run of the project's checks (`pytest`, `mypy`, `ruff` — whatever it has) on the
  UNTOUCHED tree, so later failures are attributable to your changes;
- credentials/environment verification (per the task's secret-handling rules);
- setting up your task tracking.

Never use the planning phase to get a "head start" on implementation: **creating or editing ANY
project file before the gate below is a defect**, no matter how obvious the code seems.

**HARD GATE — no project-file creation or edits until:** `paypal-plan.md` EXISTS at the repo root,
its contract sheet has **no open lookups**, and you have read it. The gate bars coding "meanwhile";
it does not bar the read-only prerequisite work above.

### Step 1c — Required reading (do this before you write any code)

End the contract sheet with a **REQUIRED READING** block whose rows carry inline `MUST load <skill>`
pointers. **Load every `python-*` skill the sheet names, now, before you start implementing** — not
lazily at the step that needs it. The sheet deliberately does *not* carry the how-to: it names the
hazard and hands you the skill that resolves it, so an unloaded pointer is a gap in what you know,
not a formality. If the sheet names none, load `python-error-handling` anyway — every integration
writes an error boundary.

These are API-agnostic usage skills; loading them is not a substitute for the lookup layer. Contract
*facts* still come only from your sheet or a fresh lookup in `python-getting-started`.

Before implementing, check the plan's **Assumptions & Blockers** section:

- Blocker or major assumption → surface it to the user in plain language, get their answer, and
  revise `paypal-plan.md` in place.
- Minor assumptions only → proceed.

Full re-planning only on genuine scope change; for a single missing fact mid-implementation, do the
lookup, never guess.

### Step 2 — Implement from the contract sheet

1. Read `paypal-plan.md` once. Treat its contracts as authoritative — do not re-derive or
   "double-check" them from memory. When a lookup revises a row, update the file so the sheet and
   the code never disagree.
2. **Decide sync vs async once, from the host application, before the first call** — and take the
   decision from the repo survey, not from preference. This SDK ships two complete client classes
   and there is no bridge between them: a sync client in an `async def` blocks the event loop, and
   an async client in sync code is a coroutine nobody awaits. Getting this wrong is not a local fix
   later; it is every call site.
3. Implement sequentially, following the repo's own conventions and layering. You loaded the
   companion skills the sheet named in Step 1c — implement each step in line with the one that
   governs it. Take every contract *fact* (signatures, wire aliases, error unions, enum members)
   from the contract sheet or a fresh lookup — never re-derive one from a companion.
4. After every change: run the project's **type checker** (`mypy`/`pyright`) as well as its tests.
   This SDK ships `py.typed` and is generated for strict checking, so a type error here is a real
   contract violation, not noise — it is the closest thing Python gives you to the compile step that
   would have caught the same mistake in C#. Fix non-SDK errors yourself.
5. **Any error involving an SDK type or member** — an `AttributeError`/`TypeError` on a
   `pay_pal_server_sdk.*` object, a `pydantic.ValidationError` you cannot place, a mypy error naming
   an SDK type, an unexpected `ApiError` — → go back to `python-getting-started`'s module map and
   read the one module that owns the fact. Do not attempt more than one self-fix of an SDK-name
   error before doing that lookup: rewriting from the same knowledge that produced the error is
   guessing.
6. Run the project's tests; verify the integration end to end the way the task demands.

### Step 3 — Answering pure questions

A standalone PayPal question with no code change: look it up in `python-getting-started` (and the
source module its map names), then give the grounded answer. Never answer from memory, even for
"easy" questions.

## Anti-patterns — never do these

- **Always load `python-getting-started` first.** It is your lookup layer, not optional background.
  (The `python-*` companions are the complement: load the ones the sheet names, per Step 1c.) Don't
  re-derive a contract *fact* from a companion.
- **Never write a PayPal/SDK fact from memory** — every signature, field name, enum member, and
  error union in your code must come from the contract sheet or a lookup. And **never write a call
  from memory "to fix later".**
- **Don't web-search PayPal topics to find an implementation detail** — the installed package's own
  source is the ground truth, and the module map tells you where to look.
- **Never introspect the SDK at runtime to discover its shape.** `dir()`, `model_fields`,
  `inspect.signature`, or a REPL poke is the Python-flavoured version of decompiling the package: it
  answers what exists, never what is *supported*, and it silently invites private attributes into
  your code. Read the source module instead.
- **Don't create or edit project files before the HARD GATE** in Step 1 — plan and contract sheet
  first, code second.
