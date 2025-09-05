# MCP Protocol Manager Rules

## Purpose
These rules govern how Claude Code should behave when the **mcp-protocol-manager** agent runs. They ensure strict separation between protocol definition and implementation concerns.

---

## Golden Rules

1. **Protocol Definition Only**
   - After the **mcp-protocol-manager** agent completes, Claude Code must NOT suggest implementation steps or server code.
   - Output should only confirm protocol schema creation and point to pending decisions for approval.

2. **No Implementation Assumptions**
   - Never propose how the protocol should be implemented in server code.
   - Never suggest tool handlers, resource providers, or business logic.

3. **Contract Boundaries**
   - Only create JSON Schema files in `contracts/mcp/` directory.
   - Never modify server implementation files or client configurations.

---

## Expected Workflow

1. **Run MCP Protocol Manager**
   - Agent creates/updates MCP protocol schemas in `contracts/mcp/`.
   - Agent logs pending decision in `docs/decisions.md`.

2. **Claude Code Output**
   - Responds with:
     - Confirmation of protocol schema creation/update
     - List of contract files created/modified
     - Reference to pending decision requiring approval

3. **Stop**
   - Do NOT propose server implementation steps.
   - Do NOT dispatch other agents.
   - Wait for explicit human approval of protocol changes.

---

## Example of Correct Post-Agent Output
```
⏺ The mcp-protocol-manager has completed its task.

- Created contracts/mcp/tools_capability.v1.json with MCP tools schema
- Updated docs/mcp-contracts.md with version tracking
- Logged pending protocol decision in docs/decisions.md

Please review the protocol schema and provide explicit approval.
```

---

## Storage & Usage
- Import into Claude Code with:
  ```
  # Additional Instructions  
  - mcp protocol manager rules @agents-rules/mcp-protocol-manager-rules.md
  ```