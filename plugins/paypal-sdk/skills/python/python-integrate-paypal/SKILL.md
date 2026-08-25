---
name: python-integrate-paypal
description: MANDATORY FIRST STEP for PayPal Python SDK work in a Python project — load this BEFORE spawning the paypal-python-sdk agent, not after; Python SDK ONLY, never load it for any other language (the C#/.NET SDK has its own router, integrate-paypal). Applies when asked to integrate PayPal in Python — take a payment at checkout, capture, refund, save a card, subscriptions, billing plans, vaulted payment methods, transaction search — or when a PayPal Python SDK call errors or behaves unexpectedly. Knowing to delegate to the paypal-python-sdk agent is NOT a substitute for loading this, because it carries five binding gates stated NOWHERE else and not inferable from the agent description — (1) the exact plan-file path you must dictate to the agent, (2) the no-project-file-edits window while the agent runs, (3) the hard gate that the plan file exists and has been read before any code, (4) the mandatory load of every python-* companion skill the contract sheet names, and (5) the map boundary, where the SDK map and paypal-python-getting-started are the agent's to open and never yours.
---

# PayPal Python SDK — Router (map + one agent)

You (the main agent) orchestrate; the `paypal-python-sdk` agent carries the SDK knowledge. The
division of labour keeps YOUR code grounded and keeps the **SDK map and source** off your context
entirely — you work from the contract sheet it returns, never from the map or source yourself. The
`python-*` companion skills are a different thing: they are API-agnostic *usage* guidance, they are
yours to load, and Step 1c below makes loading them mandatory.

## The subagent

- **`paypal-python-sdk`** is the single agent for every SDK need. It grounds in the bundled SDK map
  (and reads the installed package's source itself only when the map genuinely falls short), and it:
  - **plans** — returns a **contract sheet with no open lookups** (exact signatures, keyword-only
    boundaries, wire aliases, required-vs-`UNSET` members, the `ApiError.error` union per operation,
    and enum members) for the operations in scope; you implement from that sheet;
  - **answers** narrow contract questions directly (a field, a signature, an enum's members, which
    error union an operation carries);
  - **fixes** — hand it a runtime or type-check failure on an SDK type and it investigates from the
    map (then the one source module the map names) and fixes the code in place, running the project's
    checks to verify.

  Route EVERY SDK need — planning, a fact, an error — to this one agent. **Spawn it once; every
  later need is a follow-up message to that same warm agent.** A fresh spawn rebuilds its whole map
  context from scratch (the dominant helper cost); reuse is not optional.

**Scope guard:** the APIMatic-generated PayPal **Python SDK** (import root `pay_pal_server_sdk`,
distribution `pay-pal-server-sdk`) in **Python projects only**. Unrelated API, or any language other
than Python — do nothing; this router and its agent do not apply. For C#/.NET, the sibling router is
`integrate-paypal` with its `dotnet-*` companions.

## Workflow

**If the user opens with a reported SDK error or unexpected PayPal behaviour** (not new feature
work), spawn **`paypal-python-sdk`** directly with the traceback and the files involved, and wait —
do not run the plan-first flow for a bug report. Otherwise, for implementation work:

### Step 1 — Plan first (always, for any implementation work)

Your FIRST action is to spawn **`paypal-python-sdk`** once, with the user's full request (all
features in scope — one spawn covers the whole implementation). Dictate the output path in the
brief: the absolute path where it writes the plan (`<project repo root>/paypal-plan.md`) — do not
let it pick its own location.

It writes `paypal-plan.md` (plan + contract sheet) and returns its path.

**Parallelize the wait — prerequisites only.** Repo reconnaissance (where the integration lands in
this codebase) is not the agent's job: kick it off in the SAME message as the spawn (parallel tool
calls; background spawns if your harness has them). While the agent works, do ONLY work that needs
no SDK knowledge and touches no project file:

- the repo survey (read-only exploration of conventions, layering, and whether the project is
  sync or `async`) — brief it to return each convention as *pattern + the ONE exemplar file path to
  imitate*, NOT inline code snippets: you will read the exemplar at edit time anyway (edits need the
  file's exact current text), so a snippet dump gets paid for twice;
- **establish the toolchain before you need it**: which environment manager the repo uses (`uv`,
  `poetry`, `pip` + venv, `pdm`), whether the package is already a dependency and at what version,
  and the exact command that runs the project's tests and type checker. Getting this wrong later
  costs a broken install mid-implementation;
- a baseline run of the project's checks (`pytest`, `mypy`, `ruff` — whatever it has) on the
  UNTOUCHED tree, so later failures are attributable to your changes;
- credentials/environment verification (per the task's secret-handling rules);
- setting up your task tracking.

Never sit idle while one of these read-only prerequisites is still undone. Equally, never use the
wait to get a "head start" on implementation: **creating or editing ANY project file before the gate
below is a defect**, no matter how obvious the code seems — the agent edits files in place, so your
writes race its writes. This applies while it is running whether you spawned it OR resumed it via a
follow-up message.

**HARD GATE — no project-file creation or edits until:** the agent has RETURNED, the file EXISTS at
the path you dictated (check it — helpers have misreported save locations), and you have read it.
The gate bars coding "meanwhile"; it does not bar the read-only prerequisite work above.

### Step 1c — Required reading (do this before you write any code)

The contract sheet ends with a **REQUIRED READING** block, and its rows carry inline
`MUST load <skill>` pointers. **Load every `python-*` skill the sheet names, now, before you start
implementing** — not lazily at the step that needs it. The sheet deliberately does *not* carry the
how-to: it names the hazard and hands you the skill that resolves it, so an unloaded pointer is a
gap in what you know, not a formality. If the sheet names none, load `python-error-handling`
anyway — every integration writes an error boundary.

These are API-agnostic usage skills; loading them is not the same as reading the map, and it does
not breach the map boundary. Contract *facts* still come only from the sheet or the warm agent.

Before implementing, check the plan's **Assumptions & Blockers** section:

- Blocker or major assumption → surface it to the user in plain language, get their answer, send the
  clarification to the EXISTING agent (re-spawn only if it is gone). It revises the file in place and
  replies with the changed rows.
- Minor assumptions only → proceed.

Full re-planning only on genuine scope change; for a single missing fact mid-implementation, ask the
warm agent, never guess.

### Step 2 — Implement from the contract sheet

1. Read `paypal-plan.md` once. Treat its contracts as authoritative — do not re-derive or
   "double-check" them from memory. When the agent later revises the sheet, it replies with the
   changed rows verbatim: work from that reply, not a re-read of the file.
2. **Decide sync vs async once, from the host application, before the first call** — and take the
   decision from the repo survey, not from preference. This SDK ships two complete client classes
   and there is no bridge between them: a sync client in an `async def` blocks the event loop, and
   an async client in sync code is a coroutine nobody awaits. Getting this wrong is not a local fix
   later; it is every call site.
3. Implement sequentially, following the repo's own conventions and layering. You loaded the
   companion skills the sheet named in Step 1c — implement each step in line with the one that
   governs it. Take every contract *fact* (signatures, wire aliases, error unions, enum members)
   from the contract sheet or the warm agent — never re-derive one from a companion.
4. After every change: run the project's **type checker** (`mypy`/`pyright`) as well as its tests.
   This SDK ships `py.typed` and is generated for strict checking, so a type error here is a real
   contract violation, not noise — it is the closest thing Python gives you to the compile step that
   would have caught the same mistake in C#. Fix non-SDK errors yourself.
5. **Any error involving an SDK type or member** — an `AttributeError`/`TypeError` on a
   `pay_pal_server_sdk.*` object, a `pydantic.ValidationError` you cannot place, a mypy error naming
   an SDK type, an unexpected `ApiError` — → send the exact traceback (and the files involved) to
   your EXISTING agent as a follow-up message, NOT a new spawn, and wait. Do not attempt more than
   one self-fix of an SDK-name error before handing it over — rewriting from the same knowledge that
   produced the error is guessing.
6. Run the project's tests; verify the integration end to end the way the task demands.

### Step 3 — Answering pure questions

A standalone PayPal question with no code change: ask the warm agent (narrow-question mode) and
relay its grounded answer. Never answer from memory, even for "easy" questions.

## Wait for your agent

**Never create or edit a project file while `paypal-python-sdk` is running** — it edits files in
place, and its edits collide with yours. This holds for an agent you spawned AND one you resumed via
a follow-up message. The one thing you may do during a wait is the **read-only** Step-1 prerequisite
work — it touches no project file. When those are done and the agent is still running, wait.

## Anti-patterns — never do these

- **Get SDK knowledge from the agent, not yourself.** Don't read the installed package's source,
  don't `pip download` it, and don't web-search PayPal topics to find an implementation detail —
  that is the agent's job.
- **Don't load `paypal-python-getting-started` or the SDK map pages** — the map is the agent's, and
  loading it just bloats your context. (The `python-*` companions are the opposite case: load them,
  per Step 1c.) Don't re-derive a contract *fact* from a companion.
- **Never write a PayPal/SDK fact from memory** — every signature, field name, enum member, and
  error union in your code must come from the contract sheet or a lookup. And **never write a call
  from memory "to fix later".**
- **Never introspect the SDK at runtime to discover its shape.** `dir()`, `model_fields`,
  `inspect.signature`, or a REPL poke is the Python-flavoured version of decompiling the package: it
  answers what exists, never what is *supported*, and it silently invites private attributes into
  your code. Ask the agent.
- **Never spawn a second `paypal-python-sdk` agent.** One spawn per session; everything after is a
  follow-up message to it.
- **Don't create or edit project files while the agent runs** — see *Wait for your agent*.
