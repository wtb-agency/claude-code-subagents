# DXT Installer Tester Rules

## Purpose
These rules govern how Claude Code should behave when the **dxt-installer-tester** agent runs. They ensure strict separation between testing and implementation concerns.

---

## Golden Rules

1. **Testing Only**
   - After the **dxt-installer-tester** agent completes, Claude Code must NOT suggest extension implementation, packaging changes, or manifest updates.
   - Output should only confirm test creation and coverage validation.

2. **No Implementation Suggestions**
   - Never propose changes to extension code, manifest files, or dependency bundles.
   - Never suggest packaging improvements or distribution workflows.

3. **Test Boundaries**
   - Only modify files in `tests/dxt/` directory for installation and integration testing.
   - Never modify extension implementation, manifest files, or package files.

---

## Expected Workflow

1. **Run DXT Installer Tester**
   - Agent creates/updates test files in `tests/dxt/`.
   - Agent logs pending decision in `docs/decisions.md`.

2. **Claude Code Output**
   - Responds with:
     - Confirmation of test creation/update completion
     - List of test files created/modified with coverage summary
     - Reference to pending decision requiring approval

3. **Stop**
   - Do NOT propose implementation fixes or packaging changes.
   - Do NOT dispatch other agents.
   - Wait for explicit human approval of test changes.

---

## Example of Correct Post-Agent Output
```
⏺ The dxt-installer-tester has completed its task.

- Created tests/dxt/installation/test_file_tools_install.py
- Added tests/dxt/integration/test_claude_desktop_integration.py
- Validated cross-platform installation scenarios for Windows and macOS
- Logged pending installation testing decision in docs/decisions.md

Please review the test coverage and provide explicit approval.
```

---

## Storage & Usage
- Import into Claude Code with:
  ```
  # Additional Instructions
  - dxt installer tester rules @agents-rules/dxt-installer-tester-rules.md
  ```