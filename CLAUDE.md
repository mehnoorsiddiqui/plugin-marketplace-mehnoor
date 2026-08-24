# APIMatic Plugin Marketplace

General-purpose AI models are trained on public code and documentation, much of it outdated. They have no awareness of an actual API version, latest SDKs or the recommended workflows.

APIMatic gives coding assistants deterministic, version-aware API context, generated directly from your API definition and SDKs. Instead of scraping public documentation or guessing from memory, the AI is grounded in the exact OpenAPI definition, current SDK versions, executable, idiomatic code samples, and recommended integration workflows.

This repository is a multi-plugin marketplace (`name: apimatic`) targeting **Claude Code, Cursor, and VS Code**. It ships three plugins under `plugins/`: `context-matic`, `acp-paypal`, and `maxio-sdk`.

## Plugins

### context-matic

General-purpose, multi-API context plugin.

**MCP Server**

- `context-matic` — Get integration and implementation knowledge for third-party APIs.

**Skills**

- **integrate-context-matic** — Guidance for discovering and integrating third-party APIs using the context-matic MCP server.
- **onboard-context-matic** — Interactive onboarding tour: explains the MCP, lists available APIs, lets the user pick one to explore, demonstrates `model_search` and `endpoint_search` live, and provides a menu of suggested actions.

### acp-paypal

PayPal-focused plugin built on the same context engine.

**MCP Server**

- `acp-paypal-server-sdk-cs` — Get PayPal Server SDK (C#) integration and debugging knowledge: endpoints, models, auth, and error codes.

**Skills**

- **integrate-paypal** — Routes PayPal Server SDK tasks to the `paypal-plan` or `paypal-debug` subagent.

**Agents**

- **paypal-plan** — Read-only planner that produces a precise, SDK-contract-grounded PayPal integration plan before any code is written.
- **paypal-debug** — Diagnoses and fixes PayPal API issues in the current solution, verifying every change against the MCP server.

### maxio-sdk

Maxio Advanced Billing (formerly Chargify) **.NET SDK** plugin — no MCP server, no telemetry,
Claude Code only, C#/.NET only. Its core feature is a bundled, generated **SDK map**
(`skills/maxio-getting-started/sdk-map.md` + `map/`) plus a subagent orchestration layer: the agent
answers every signature/model/enum/error question by map lookup, clones the SDK source **only on
first need** for a full body the map doesn't carry, and never greps the clone or opens the SDK's
`api-reference.md`.

**Skills**

- **integrate-maxio** — orchestrator/router: routes a Maxio .NET SDK task to the `maxio-plan` or
  `maxio-debug` agent, handles blocker hand-back, and drives the implement-and-verify loop.
- **maxio-getting-started** — SDK-specific entry point: identity, client construction, servers/auth,
  the SDK map, lookup hygiene ("keep lookups cheap"), and the contract-sheet workflow.
- Seven `dotnet-*` companions (`dotnet-client-initialization`, `dotnet-authentication`,
  `dotnet-calling-endpoints`, `dotnet-models`, `dotnet-error-handling`,
  `dotnet-configuration-resilience`, `dotnet-testing`) — usage guidance layered on the map.

**Agents**

- **maxio-plan** — read-only planner: loads the bundled skills + SDK map and writes a
  contract-grounded `maxio-plan.md` before any code is written (no MCP).
- **maxio-debug** — diagnoses and fixes Maxio code in place, map-first, verifying with `dotnet build`
  / `dotnet test` (no MCP).

The map's generated pages are never hand-edited — they are produced by the
`sdk-map-generator` repo and verified field-exact against the SDK source
(github.com/asadali214/advanced-billing-sample-sdk, pinned per map stamp).

## Per-IDE manifest convention

MCP-backed plugins carry one manifest per IDE, and each manifest points at its own MCP config file so it can send an IDE-specific `X-Apimatic-Mcp-Client` telemetry header:

- Claude Code: `.claude-plugin/plugin.json` → `.claude-mcp.json` (header `ClaudeCode`)
- Cursor: `.cursor-plugin/plugin.json` → `.cursor-mcp.json` (header `Cursor`)
- VS Code: root `plugin.json` (Copilot format) → `.mcp.json` (header `VSCode`)

(The maxio-sdk plugin is the exception: it has no MCP server and ships only the Claude Code manifest.)
