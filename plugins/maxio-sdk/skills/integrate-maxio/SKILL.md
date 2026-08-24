---
name: integrate-maxio
description: 'Entry point and router for all Maxio Advanced Billing .NET SDK work — routes planning to the maxio-plan subagent (which returns a map-grounded contract sheet) and SDK errors to the maxio-debug subagent. Use when the user asks to integrate Maxio (formerly Chargify) billing, implement subscriptions/customers/usage/invoices with the Maxio .NET SDK, or reports a Maxio error or unexpected SDK behaviour in a C#/.NET project. Main agent: you never read, clone, grep, or decompile SDK source or the compiled/NuGet package, and you never handle the SDK clone or its path — that is the job of the plan and debug agents; never create or edit a project file while plan or debug is running, because it edits files in place and its edits collide with yours.'
---

# Maxio Advanced Billing .NET SDK — Router (map + subagents)

You (the main agent) orchestrate; the subagents carry the SDK knowledge. The division of
labour keeps YOUR context small and YOUR code grounded — you never touch the SDK yourself.

## The subagents

- **`maxio-plan`** does all SDK contract discovery against the bundled map and returns a
  **contract sheet with no open lookups** — exact signatures, wire names, envelope shapes,
  error accessors, and enum values for the operations in scope. (A fact the map genuinely
  cannot carry becomes a defensive directive or a row flagged for `maxio-debug`, never left
  open.) You implement from that sheet; route every SDK contract need to it.
- **`maxio-debug`** owns SDK-related build/runtime failures — any error naming an SDK
  symbol, or any unexpected Maxio behaviour — and is the only agent that opens SDK source
  (map row first; it clones the source itself only when the map genuinely falls short). It
  fixes the code in place and reports what changed.

**Scope guard:** Maxio Advanced Billing .NET SDK (`AsadAli.AdvancedBilling.Sdk`,
namespace `MaxioAdvancedBilling`) in C#/.NET projects only. Unrelated API or language —
do nothing.

## Workflow

**If the user opens with a reported SDK error or unexpected Maxio behaviour** (not new feature work),
spawn **`maxio-debug`** directly with the error output and the files involved, and wait — do not run the
plan-first flow for a bug report. Otherwise, for implementation work:

### Step 1 — Plan first (always, for any implementation work)

Your FIRST action is to spawn **`maxio-plan`** once, with the user's full request (all
features in scope — one spawn covers the whole implementation). Dictate the output path
in the brief: the absolute path where it writes the plan (`<project repo
root>/maxio-plan.md`) — do not let the helper pick its own location.

It writes `maxio-plan.md` (plan + contract sheet) and returns its path.

**Parallelize the wait — prerequisites only.** Repo reconnaissance (where the
integration lands in this codebase) is not `maxio-plan`'s job: kick it off in the
SAME message as the `maxio-plan` spawn (parallel tool calls; background spawns if
your harness has them). While `maxio-plan` works, do ONLY work that needs no SDK
knowledge and touches no project file:

- the repo survey (read-only exploration of conventions and layering) — brief it to
  return each convention as *pattern + the ONE exemplar file path to imitate*, NOT
  inline code snippets: you will Read the exemplar at edit time anyway (edits need
  the file's exact current text), so a snippet dump gets paid for twice;
- `dotnet restore` and a baseline `dotnet build` / `dotnet test` of the UNTOUCHED
  solution, so later failures are attributable to your changes;
- locating the SDK package and its version in the project;
- credentials/environment verification (per the task's secret-handling rules);
- setting up your task tracking.

Never sit idle while one of these read-only prerequisites is still undone. Equally,
never use the wait to get a "head start" on implementation: **creating or editing ANY
project file before the gate below is a defect**, no matter how obvious the code
seems — the plan agent (and later `maxio-debug`) edits files in place, so your writes
race its writes. This applies while a helper is running whether you spawned it OR
resumed it via a follow-up message — a resumed or backgrounded helper is still running.

**HARD GATE — no project-file creation or edits until:** the plan agent has
RETURNED, the file EXISTS at the path you dictated (check it — helpers have
misreported save locations), and you have read it. The gate bars coding
"meanwhile"; it does not bar the read-only prerequisite work above. Starting to
code before the sheet exists defeats the entire plan-first design.

Before implementing, check the plan's **Assumptions & Blockers** section:
- Blocker or major assumption → surface it to the user in plain language, get their
  answer, send the clarification to the EXISTING `maxio-plan` agent (re-spawn only if it
  is gone). It revises the file in place and replies with the changed rows.
- Minor assumptions only → proceed.

Full re-planning only on genuine scope change; for a single missing fact mid-implementation
see *Anti-patterns* — ask the warm plan agent, never guess.

### Step 2 — Implement from the contract sheet

1. Read `maxio-plan.md` once. Treat its contracts as authoritative — do not re-derive or
   "double-check" them from memory. When a helper later revises the sheet, it replies with
   the changed rows verbatim: work from that reply, not a re-read of the file.
2. Implement sequentially, following the repo's own conventions and layering.
3. After every change: `dotnet build`; fix non-SDK errors yourself.
4. **Any compile or runtime error involving an SDK type or member** (`CS1061`, `CS0117`,
   `CS0234`, `CS0104`, `CS1503`, `CS7036`, … on `MaxioAdvancedBilling.*`, or a provider
   error at runtime) → spawn **`maxio-debug`** with the exact error output and the files
   involved, and wait for it. Do not attempt more than one self-fix of an SDK-name error
   before escalating — rewriting from the same knowledge that produced the error is
   guessing.
5. Run the project's tests (`dotnet test`); verify the integration end to end the way the
   task demands.

**Reuse, don't re-spawn.** A fresh spawn rebuilds a prompt cache from scratch (the dominant
helper cost). If your harness can send a follow-up message to an already-spawned agent,
direct follow-up questions and plan revisions to the EXISTING `maxio-plan` agent — its map
context is already warm. Spawn fresh only when that isn't available or the agent is gone.
Require tight returns; if one arrives bloated, use what you need and drop the rest — never
paste a helper's reference dump into files or carry it forward.

### Step 3 — Answering pure questions

A standalone Maxio question with no code change: spawn `maxio-plan` in narrow-question mode
and relay its grounded answer. Never answer from memory, even for "easy" questions.

## Wait for your agents

The race rule is precise: **never create or edit a project file while a helper is
running** — `maxio-plan`, and especially `maxio-debug`, edit files in place, and their
edits collide with yours. This holds for a helper you spawned AND one you resumed via a
follow-up message (a resumed or backgrounded helper is still running). The one thing you
may do during a wait is the **read-only** Step-1 prerequisite work (repo survey, restore,
baseline build, env checks) — it touches no project file. When those are done and a helper
is still running, wait.

## Anti-patterns — never do these

Everything the main agent must NOT do is collected here, in one place:

- **Never touch the SDK source yourself.** Don't read, clone, grep, `find`, or otherwise
  ingest the SDK source to find an implementation detail — that is `maxio-plan`'s job (for
  planning) and `maxio-debug`'s (for failures).
- **Don't use the compiled SDK either.** No decompiling or reflecting over the
  installed/NuGet package to find implementation.
- **Don't handle the clone.** The SDK clone and its filesystem path belong to
  `maxio-debug`; you never read the clone and never need or receive its path.
- **Never open `map/models/records-*.md` yourself.** They are the largest pages in the map
  and would ride in your context for every remaining turn.
- **Don't bulk-load reference material.** Don't load `maxio-getting-started`, the `dotnet-*`
  skills, or the SDK map pages, and don't carry reference dumps — the contract sheet is
  your working reference. (The one allowed exception is the single targeted map-page read
  below.)
- **Don't open the SDK's `api-reference.md`, and don't web-search Maxio topics.**
- **Never write a Maxio/SDK fact from memory** — every signature, field name, enum value,
  and error type in your code must come from the contract sheet or a lookup. And **never
  write a call from memory "to fix later".**
- **Don't re-derive or double-check a sheet row from memory.** If you are unsure what a row
  said, either batch your questions into ONE message to the warm `maxio-plan` agent (it
  holds the map pages warm and answers in seconds), or make ONE targeted read of just the
  map page that row cites — never `map/models/records-*.md`.
- **Don't create or edit project files while a helper runs** — see *Wait for your agents*
  (read-only Step-1 prerequisites are the one exception).
