# DXT Packager Rules

## Purpose
These rules govern how Claude Code should behave when the **dxt-packager** agent runs. They ensure strict separation between packaging and other DXT concerns.

---

## Golden Rules

1. **Packaging Only**
   - After the **dxt-packager** agent completes, Claude Code must NOT suggest extension implementation, manifest changes, or dependency bundling.
   - Output should only confirm .dxt package creation and validation.

2. **No Component Creation**
   - Never propose changes to extension code, manifest files, or dependency bundles.
   - Never suggest installation testing or deployment workflows.

3. **Package Boundaries**
   - Only modify files in `dist/dxt/` directory for package creation.
   - Never modify extension implementation, manifest files, or dependency bundles.

---

## Expected Workflow

1. **Run DXT Packager**
   - Agent creates/updates .dxt packages in `dist/dxt/`.
   - Agent logs pending decision in `docs/decisions.md`.

2. **Claude Code Output**
   - Responds with:
     - Confirmation of package creation completion
     - List of .dxt files created with checksums
     - Reference to pending decision requiring approval

3. **Stop**
   - Do NOT propose installation testing or distribution steps.
   - Do NOT dispatch other agents.
   - Wait for explicit human approval of package creation.

---

## Example of Correct Post-Agent Output
```
⏺ The dxt-packager has completed its task.

- Created dist/dxt/file-tools/file-tools-v1.0.0.dxt package
- Generated checksums and integrity verification
- Validated package structure and DXT specification compliance
- Logged pending package creation decision in docs/decisions.md

Please review the .dxt package and provide explicit approval.
```

---

## Storage & Usage
- Import into Claude Code with:
  ```
  # Additional Instructions
  - dxt packager rules @agents-rules/dxt-packager-rules.md
  ```