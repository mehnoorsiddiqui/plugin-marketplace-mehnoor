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

Maxio Advanced Billing (formerly Chargify) **Python SDK** plugin — no MCP server, no telemetry,
no agents, Claude Code only, Python only. Its core feature is a bundled, generated **SDK map**
(`skills/python-getting-started/sdk-map.md` + `map/operations/`, 34 controller pages covering 250
operations): every signature/model/enum/error question is answered by map lookup, opening only the
one module a row names, and never grepping the package tree.

**Skills**

- **python-integrate-maxio** — the router. Plan-first, with a hard gate: no project file is created
  or edited until `maxio-plan.md` exists at the repo root carrying a contract sheet with no open
  lookups, and has been read.
- **python-getting-started** — SDK-specific entry point: identity, install, client construction, the
  three servers and their `{site}`/`{connector}` template variables, the two auth schemes, the error
  model, the bundled SDK map, and what a contract sheet for this SDK must carry.
- Ten `python-*` companions (`python-integration-planning`, `python-client-initialization`,
  `python-authentication`, `python-calling-endpoints`, `python-models`, `python-error-handling`,
  `python-configuration-resilience`, `python-inbound-state`, `python-sdk-drift`, `python-testing`)
  — API-agnostic usage guidance layered on the map, applying to any APIMatic Python SDK.

`python-integration-planning`, `python-inbound-state` and `python-sdk-drift` close the readiness rows
the SDK surface never raises on its own: what must be true before shipping, what the provider sends
back (Maxio has webhooks), and what breaks silently when the SDK is regenerated.

The map's generated pages are never hand-edited — they are produced with the SDK and verified
field-exact against its source.

## Per-IDE manifest convention

MCP-backed plugins carry one manifest per IDE, and each manifest points at its own MCP config file so it can send an IDE-specific `X-Apimatic-Mcp-Client` telemetry header:

- Claude Code: `.claude-plugin/plugin.json` → `.claude-mcp.json` (header `ClaudeCode`)
- Cursor: `.cursor-plugin/plugin.json` → `.cursor-mcp.json` (header `Cursor`)
- VS Code: root `plugin.json` (Copilot format) → `.mcp.json` (header `VSCode`)

(The maxio-sdk plugin is the exception: it has no MCP server and ships only the Claude Code manifest.)
