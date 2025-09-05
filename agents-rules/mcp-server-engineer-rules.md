# MCP Server Engineer Rules

## Purpose
These rules govern how Claude Code should behave when the **mcp-server-engineer** agent runs. They ensure strict separation between server implementation and other MCP concerns.

---

## Golden Rules

1. **Implementation Only**
   - After the **mcp-server-engineer** agent completes, Claude Code must NOT suggest protocol changes, client configs, or testing approaches.
   - Output should only confirm server implementation and point to pending decisions for approval.

2. **No Architecture Expansion**
   - Never propose additional server features beyond the approved contracts.
   - Never suggest tool handlers, resource providers, or client integrations.

3. **Server Code Boundaries**
   - Only modify files in `src/mcp/` directory for server implementation.
   - Never modify protocol contracts, client configs, or test files.

---

## Expected Workflow

1. **Run MCP Server Engineer**
   - Agent creates/updates server implementation in `src/mcp/`.
   - Agent logs pending decision in `docs/decisions.md`.

2. **Claude Code Output**
   - Responds with:
     - Confirmation of server implementation completion
     - List of server files created/modified
     - Reference to pending decision requiring approval

3. **Stop**
   - Do NOT propose protocol updates or client configurations.
   - Do NOT dispatch other agents.
   - Wait for explicit human approval of server implementation.

---

## Example of Correct Post-Agent Output
```
⏺ The mcp-server-engineer has completed its task.

- Implemented src/mcp/server/message_router.py with MCP protocol handling
- Created src/mcp/transport/stdio_transport.py for stdio communication
- Logged pending server implementation decision in docs/decisions.md

Please review the server implementation and provide explicit approval.
```

---

## Storage & Usage
- Import into Claude Code with:
  ```
  # Additional Instructions
  - mcp server engineer rules @agents-rules/mcp-server-engineer-rules.md
  ```