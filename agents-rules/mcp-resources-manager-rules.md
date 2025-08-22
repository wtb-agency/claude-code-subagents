# MCP Resources Manager Rules

## Purpose
These rules govern how Claude Code should behave when the **mcp-resources-manager** agent runs. They ensure strict separation between resource definition/implementation and other MCP concerns.

---

## Golden Rules

1. **Resource-Specific Focus**
   - After the **mcp-resources-manager** agent completes, Claude Code must NOT suggest server routing, tool handlers, or client configurations.
   - Output should only confirm resource schema and provider implementation.

2. **No Capability Expansion**
   - Never propose additional MCP capabilities beyond resources.
   - Never suggest tool handlers, protocol changes, or integration configs.

3. **Resource Boundaries**
   - Only modify files in `contracts/mcp/resources/` and `src/mcp/resources/` directories.
   - Never modify server routing, transport layers, or client configurations.

---

## Expected Workflow

1. **Run MCP Resources Manager**
   - Agent creates/updates resource schemas in `contracts/mcp/resources/`.
   - Agent creates/updates resource providers in `src/mcp/resources/`.
   - Agent logs pending decision in `docs/decisions.md`.

2. **Claude Code Output**
   - Responds with:
     - Confirmation of resource schema and provider completion
     - List of resource-related files created/modified
     - Reference to pending decision requiring approval

3. **Stop**
   - Do NOT propose server architecture changes or tool handlers.
   - Do NOT dispatch other agents.
   - Wait for explicit human approval of resource changes.

---

## Example of Correct Post-Agent Output
```
⏺ The mcp-resources-manager has completed its task.

- Created contracts/mcp/resources/file_resource.v1.json resource schema
- Implemented src/mcp/resources/filesystem/file_provider.py provider
- Updated src/mcp/resources/registry.py with resource registration
- Logged pending resource update decision in docs/decisions.md

Please review the resource implementation and provide explicit approval.
```

---

## Storage & Usage
- Import into Claude Code with:
  ```
  # Additional Instructions
  - mcp resources manager rules @agents-rules/mcp-resources-manager-rules.md
  ```