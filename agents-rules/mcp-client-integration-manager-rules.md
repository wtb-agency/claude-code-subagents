# MCP Client Integration Manager Rules

## Purpose
These rules govern how Claude Code should behave when the **mcp-client-integration-manager** agent runs. They ensure strict separation between client integration and server implementation concerns.

---

## Golden Rules

1. **Client Configuration Only**
   - After the **mcp-client-integration-manager** agent completes, Claude Code must NOT suggest server implementation, protocol changes, or tool development.
   - Output should only confirm client configuration and integration guide creation.

2. **No Server Implementation**
   - Never propose server-side changes, tool handlers, or resource providers.
   - Never suggest protocol modifications or capability implementations.

3. **Client Boundaries**
   - Only modify files in `configs/mcp-clients/` and `docs/mcp-integration/` directories.
   - Never modify server implementation, protocol contracts, or test files.

---

## Expected Workflow

1. **Run MCP Client Integration Manager**
   - Agent creates/updates client configurations in `configs/mcp-clients/`.
   - Agent creates/updates integration documentation in `docs/mcp-integration/`.
   - Agent logs pending decision in `docs/decisions.md`.

2. **Claude Code Output**
   - Responds with:
     - Confirmation of client configuration completion
     - List of client-related files created/modified
     - Reference to pending decision requiring approval

3. **Stop**
   - Do NOT propose server implementation or tool development.
   - Do NOT dispatch other agents.
   - Wait for explicit human approval of client integration changes.

---

## Example of Correct Post-Agent Output
```
⏺ The mcp-client-integration-manager has completed its task.

- Created configs/mcp-clients/claude-desktop.json configuration
- Created docs/mcp-integration/claude-desktop-setup.md installation guide
- Updated docs/mcp-integration/compatibility-matrix.md with client support
- Logged pending client integration decision in docs/decisions.md

Please review the client integration and provide explicit approval.
```

---

## Storage & Usage
- Import into Claude Code with:
  ```
  # Additional Instructions
  - mcp client integration manager rules @agents-rules/mcp-client-integration-manager-rules.md
  ```