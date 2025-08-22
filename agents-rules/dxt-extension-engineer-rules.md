# DXT Extension Engineer Rules

## Purpose
These rules govern how Claude Code should behave when the **dxt-extension-engineer** agent runs. They ensure strict separation between extension implementation and other DXT concerns.

---

## Golden Rules

1. **Implementation Only**
   - After the **dxt-extension-engineer** agent completes, Claude Code must NOT suggest manifest changes, dependency bundling, or packaging approaches.
   - Output should only confirm extension implementation and point to pending decisions for approval.

2. **No Architecture Expansion**
   - Never propose additional extension features beyond the approved contracts.
   - Never suggest manifest updates, configuration changes, or packaging workflows.

3. **Extension Code Boundaries**
   - Only modify files in `src/dxt/` directory for extension implementation.
   - Never modify manifest files, configuration contracts, or dependency bundles.

---

## Expected Workflow

1. **Run DXT Extension Engineer**
   - Agent creates/updates extension implementation in `src/dxt/`.
   - Agent logs pending decision in `docs/decisions.md`.

2. **Claude Code Output**
   - Responds with:
     - Confirmation of extension implementation completion
     - List of extension files created/modified
     - Reference to pending decision requiring approval

3. **Stop**
   - Do NOT propose manifest updates or dependency changes.
   - Do NOT dispatch other agents.
   - Wait for explicit human approval of extension implementation.

---

## Example of Correct Post-Agent Output
```
⏺ The dxt-extension-engineer has completed its task.

- Implemented src/dxt/file-tools/server.py with MCP integration
- Created src/dxt/file-tools/handlers/ with tool implementations
- Added cross-platform file handling with proper error management
- Logged pending extension implementation decision in docs/decisions.md

Please review the extension implementation and provide explicit approval.
```

---

## Storage & Usage
- Import into Claude Code with:
  ```
  # Additional Instructions
  - dxt extension engineer rules @agents-rules/dxt-extension-engineer-rules.md
  ```