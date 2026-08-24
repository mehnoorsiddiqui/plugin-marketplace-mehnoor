---
name: integrate-paypal
description: 'Routes PayPal Server SDK tasks to the paypal-plan or paypal-debug subagent. Use when the user asks to integrate PayPal, implement PayPal payments, or reports a PayPal error or unexpected SDK behaviour in their project (any language).'
---

# PayPal Server SDK — Skill Router

This skill routes PayPal *planning* to the `paypal-plan` subagent and PayPal *debugging* to the `paypal-debug` subagent. You (the main agent) then implement the plan `paypal-plan` produces (Step 2) and may answer quick PayPal questions directly — but never work from your own SDK knowledge: ground every PayPal fact in the MCP server (see below).

**Scope guard:** Only apply this skill when the user is working on PayPal or PayPal Server SDK integration. When the user is doing something unrelated, do nothing.

## Grounding in the MCP Server — Never Guess About PayPal

Your training data on the PayPal Server SDK is stale and must never be used as a source of truth. The `acp-paypal-server-sdk-ts` MCP server is the authoritative source for every PayPal SDK fact. **Whenever you are uncertain about anything PayPal-related — even if you think you know the answer — consult the MCP server instead of guessing.**

The subagents (`paypal-plan`, `paypal-debug`) do this during their runs. You also call these tools directly for **quick lookups** — to route correctly, to answer a standalone question, or to fill a small gap while implementing a plan. But use **paypal-plan** for any actual *planning*, and implement the plan it returns rather than re-deriving it (Step 1 is the single source for when to spawn or re-spawn it).

| Tool | Use it when you need to… |
|---|---|
| **ask** | Understand a concept or workflow in plain language — authentication flows, how orders/captures/subscriptions/webhooks work, feature behaviour, recommended patterns, or "is X possible with this SDK?". Break broad questions into focused ones (e.g. _"How does order capture work?"_, _"How are webhooks verified?"_). |
| **endpoint_search** | Confirm an exact SDK method's name, controller, parameters, return type, or error codes (e.g. the create-order / capture-order operations). Use case-sensitive exact or partial method names, matching the SDK's casing convention for the project's language (PascalCase, camelCase, or snake_case). |
| **model_search** | Confirm a request/response model's fields, types, required vs. optional, and enum values (e.g. `OrderRequest`, `Money`, `LinkDescription`). Use case-sensitive exact or partial model names. |

Guidance:
- If a user's PayPal question can be answered from the MCP server without code changes, answer it directly using **ask** (and **endpoint_search** / **model_search** for specifics) — you do not have to spawn an agent for a pure question.
- If the question turns into actual integration or debugging work, route to the appropriate agent per the workflow below.
- Resolve PayPal ambiguity through MCP lookups first; only ask the user when the ambiguity is about *their* intent, not about how the SDK works.
- Keep only the SDK code snippets from an MCP response that help write the integration; drop doc-link reference dumps rather than carrying them into your context or relaying them.

### Tool names and the operational tool

All tools this skill uses are MCP tools on the `acp-paypal-server-sdk-ts` server, addressed with the `mcp__acp-paypal-server-sdk-ts__` prefix (e.g. `ask` → `mcp__acp-paypal-server-sdk-ts__ask`). Besides the three lookup tools above, the workflow uses one **operational** tool on the same server: **update_activity**, to report integration milestones on the plan/implementation path (Step 2). If the connected server does not expose it, skip milestone reporting rather than fabricating a substitute.

## When to Apply

Apply this skill when the user:
- Asks to integrate PayPal into their project
- Wants to implement PayPal payments, orders, subscriptions, payouts, or webhooks
- Reports a PayPal error, failed payment, incorrect API response, or unexpected SDK behaviour
- Needs to maintain or change existing PayPal code — SDK version upgrades, deprecated fields, or new parameters on existing calls
- Mentions PayPal implementation, integration, or debugging

## Workflow

### Step 1 — Select and Spawn the Agent

| Agent | When to spawn |
|---|---|
| **paypal-plan** | User wants to add or implement any PayPal feature (payments, orders, subscriptions, payouts, webhooks), **or maintain/change existing PayPal code** (SDK upgrades, deprecated fields, new parameters). Produces a detailed API work plan (endpoints, SDK methods, models, auth) that you implement in **Step 2**. For maintenance, ask `paypal-plan` for a plan covering the change, then implement it. |
| **paypal-debug** | User is experiencing errors, unexpected behaviour, failed payments, incorrect API responses, or SDK misuse. |

- **One spawn per implementation.** Spawn `paypal-plan` **once** for the whole implementation and pass it the user's full request — regardless of how many PayPal features it involves. It produces a single plan covering the entire scope, looking up shared auth/models only once. Do not spawn it again to cover additional features of the same implementation.
- Re-spawn `paypal-plan` only when something forces a *new* plan: an ambiguity or blocker the user has now resolved (see the blocker handling below), or a genuine scope change introducing PayPal operations the plan never covered. For small gaps mid-implementation, prefer a quick direct MCP lookup over a re-spawn (see Step 2).
- **paypal-plan** returns an API work plan — before incorporating it, inspect the Assumptions and Blockers section:
  - **If a blocker is present, or an assumption is potentially incorrect or major in impact:** Stop, surface it to the user in plain language, and ask them to confirm or correct it. Then re-spawn **paypal-plan**, telling it the existing `paypal-plan.md` is present and appending the user's clarification, so it revises only the affected sections instead of re-planning from scratch. Do not proceed to implementation until the plan is free of unresolved blockers and major assumptions.
  - **If assumptions are minor and no blockers exist:** Incorporate the plan into your master plan alongside the repo/implementation work and proceed to **Step 2**.
- **paypal-debug** returns a root-cause summary and fix. Before relaying, inspect the Unresolved blockers section:
  - **If a blocker is present:** Stop, surface it to the user in plain language, and ask them to confirm or provide what's needed. Then re-spawn **paypal-debug** with the user's answer appended to the original prompt. Repeat until no unresolved blockers remain.
  - **If there are no unresolved blockers:** Relay the summary and fix to the user.
- If the task is ambiguous, default to **paypal-plan**.

### Step 2 — Implement and Verify (paypal-plan path)

Applies after a `paypal-plan` plan is accepted — for a new integration or for maintenance of existing PayPal code. (`paypal-debug` implements and verifies its own fixes, so this step does not apply to the debug path.)

1. **Read `paypal-plan.md`** at the path the agent returned, then **implement it as returned**, treating its SDK contracts as authoritative. Work through the features/milestones sequentially against that one file; read it once up front — you do not need to re-read it in full at each milestone. Follow the project's established conventions and any security/test guidelines already present in the repo.
2. If a small SDK detail is missing, make a quick MCP lookup yourself (`ask` / `endpoint_search` / `model_search`) rather than re-spawning. Re-spawn `paypal-plan` only per Step 1 (a resolved ambiguity, or genuinely out-of-scope operations the plan never covered).
3. **Report milestones** by calling **update_activity** the moment each is concretely reached in code (not when merely planned); do not batch or defer. On this plan/implementation path these milestones are **yours** to fire — `paypal-plan` has no `update_activity` tool and never fires them. (On the debug path you fire none: `paypal-debug` owns its own milestones — see the scope note at the top of Step 2.) Fire each of these the moment it's reached:

   | Milestone | When to call |
   |---|---|
   | `sdk_setup` | SDK package installed and confirmed via the project's package manager. |
   | `auth_configured` | PayPal credentials written into the project's runtime environment **and** referenced in actual code. |
   | `first_call_made` | First PayPal API call code written and executed. |

4. **Verify:** after every code change, run the project's build/compile step and fix all errors; then run any tests covering the integration.
5. If you hit a PayPal-specific error you cannot resolve from the plan or a quick lookup, spawn **paypal-debug** with the details rather than guessing.

## Notes

- The subagents own the work inside their runs: their own MCP lookups, code changes, compilation, and milestones. The main agent uses **paypal-plan** for planning and implements the returned plan; it may call the MCP server directly for quick lookups, and on the integration/maintenance path it fires the integration milestones and verifies the build itself (Step 2).

### Grounding rules (main agent)

- Treat the SDK contracts in a returned `paypal-plan`/`paypal-debug` result as **authoritative**. Do not independently re-verify or re-derive them — the agent already grounded them against the MCP server.
- **Do not inspect, reflect over, decompile, or grep the installed SDK package as a substitute for the MCP** — the acp-paypal MCP server is the source of truth and you must consult it first. Only when a specific fact genuinely cannot be resolved via the MCP (after trying `ask` / `endpoint_search` / `model_search`) may you fall back to a light, targeted check of the installed package — a single quick lookup of the specific type or member in its source or type-definition files, never a full crawl or reflection-based discovery.
- To resolve an item the plan flags as open, surface it to the user, query the MCP server (`ask` / `model_search` / `endpoint_search`), or re-spawn `paypal-plan` per Step 1; only as a last resort, after the MCP cannot resolve it, use the light, targeted check described above.
