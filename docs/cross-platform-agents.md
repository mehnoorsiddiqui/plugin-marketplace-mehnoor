CREATING AN AGENT FILE
──────────────────────
Minimum required fields: name + description
File extension:          .md (all three tools)
                         .agent.md (VS Code Copilot format also works)
Body:                    The system prompt, in Markdown

SCOPES (where to put standalone files)
───────────────────────────────────────
Project   → .claude/agents/      (Claude Code, VS Code, Cursor all read this)
User      → ~/.claude/agents/    (Claude Code, VS Code)
           ~/.cursor/agents/     (Cursor)

IN A PLUGIN
───────────
Agent files → agents/ directory at plugin root
Manifests   → .claude-plugin/plugin.json   (Claude Code)
              .cursor-plugin/plugin.json   (Cursor)
              plugin.json at root          (VS Code)

TOOL RESTRICTION
────────────────
Claude Code  → tools: Read, Grep     (allowlist, comma string)
               disallowedTools: Write (denylist, comma string)
VS Code      → tools: ['search/codebase']  (array syntax)
Cursor       → readonly: true              (binary only)

HANDOFFS
────────
VS Code      → handoffs: [{label, agent, prompt, send, model}]
Claude Code  → prompt-driven only: "use X subagent then Y subagent"
Cursor       → prompt-driven only: /agent-name or natural language

PLUGIN AGENT RESTRICTIONS
─────────────────────────
Claude Code  → hooks, mcpServers, permissionMode are STRIPPED from plugin agents
VS Code      → no restrictions
Cursor       → no restrictions (hooks in agent files not supported anyway)