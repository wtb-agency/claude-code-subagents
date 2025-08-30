# DXT Manifest Manager Rules

## Purpose
These rules govern how Claude Code should behave when the **dxt-manifest-manager** agent runs. They ensure strict separation between manifest definition and implementation concerns.

---

## Golden Rules

1. **Manifest Definition Only**
   - After the **dxt-manifest-manager** agent completes, Claude Code must NOT suggest extension implementation, dependency bundling, or packaging steps.
   - Output should only confirm manifest creation and point to pending decisions for approval.

2. **No Implementation Assumptions**
   - Never propose how the manifest should be implemented in extension code.
   - Never suggest dependency management or packaging workflows.

3. **Manifest Boundaries**
   - Only create manifest.json files in `configs/dxt/` directory.
   - Never modify extension implementation files or dependency bundles.

---

## Expected Workflow

1. **Run DXT Manifest Manager**
   - Agent creates/updates extension manifests in `configs/dxt/`.
   - Agent logs pending decision in `docs/decisions.md`.

2. **Claude Code Output**
   - Responds with:
     - Confirmation of manifest creation/update
     - List of manifest files created/modified
     - Reference to pending decision requiring approval

3. **Stop**
   - Do NOT propose extension implementation steps.
   - Do NOT dispatch other agents.
   - Wait for explicit human approval of manifest changes.

---

## Example of Correct Post-Agent Output
```
⏺ The dxt-manifest-manager has completed its task.

- Created configs/dxt/file-tools/manifest.json with DXT specification compliance
- Updated docs/dxt-manifests.md with version tracking
- Logged pending manifest decision in docs/decisions.md

Please review the extension manifest and provide explicit approval.
```

---

## Storage & Usage
- Import into Claude Code with:
  ```
  # Additional Instructions
  - dxt manifest manager rules @agents-rules/dxt-manifest-manager-rules.md
  ```