# MCP Tools Manager Rules

## Purpose
These rules govern how Claude Code should behave when the **mcp-tools-manager** agent runs. They ensure strict separation between tool definition/implementation and other MCP concerns.

---

## Golden Rules

1. **Tool-Specific Focus**
   - After the **mcp-tools-manager** agent completes, Claude Code must NOT suggest server routing, transport changes, or client configurations.
   - Output should only confirm tool schema and handler implementation.

2. **No Capability Expansion**
   - Never propose additional MCP capabilities beyond tools.
   - Never suggest resource providers, protocol changes, or integration configs.

3. **Tool Boundaries**
   - Only modify files in `contracts/mcp/tools/` and `src/mcp/tools/` directories.
   - Never modify server routing, transport layers, or client configurations.

---

## Expected Workflow

1. **Run MCP Tools Manager**
   - Agent creates/updates tool schemas in `contracts/mcp/tools/`.
   - Agent creates/updates tool handlers in `src/mcp/tools/`.
   - Agent logs pending decision in `docs/decisions.md`.

2. **Claude Code Output**
   - Responds with:
     - Confirmation of tool schema and handler completion
     - List of tool-related files created/modified
     - Reference to pending decision requiring approval

3. **Stop**
   - Do NOT propose server architecture changes or resource providers.
   - Do NOT dispatch other agents.
   - Wait for explicit human approval of tool changes.

---

## Example of Correct Post-Agent Output
```
⏺ The mcp-tools-manager has completed its task.

- Created contracts/mcp/tools/read_file.v1.json tool schema
- Implemented src/mcp/tools/filesystem/read_file.py handler
- Updated src/mcp/tools/registry.py with tool registration
- Logged pending tool update decision in docs/decisions.md

Please review the tool implementation and provide explicit approval.
```

---

## Storage & Usage
- Import into Claude Code with:
  ```
  # Additional Instructions
  - mcp tools manager rules @agents-rules/mcp-tools-manager-rules.md
  ```