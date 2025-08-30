# DXT Dependency Bundler Rules

## Purpose
These rules govern how Claude Code should behave when the **dxt-dependency-bundler** agent runs. They ensure strict separation between dependency management and other DXT concerns.

---

## Golden Rules

1. **Dependency Bundling Only**
   - After the **dxt-dependency-bundler** agent completes, Claude Code must NOT suggest extension implementation, manifest changes, or packaging approaches.
   - Output should only confirm dependency bundling and optimization.

2. **No Implementation Suggestions**
   - Never propose changes to extension code or manifest files.
   - Never suggest packaging workflows or installation testing.

3. **Dependency Boundaries**
   - Only modify files in `deps/dxt/` directory for dependency management.
   - Never modify extension implementation, manifest files, or package files.

---

## Expected Workflow

1. **Run DXT Dependency Bundler**
   - Agent creates/updates dependency bundles in `deps/dxt/`.
   - Agent logs pending decision in `docs/decisions.md`.

2. **Claude Code Output**
   - Responds with:
     - Confirmation of dependency bundling completion
     - List of dependency bundles created/modified
     - Reference to pending decision requiring approval

3. **Stop**
   - Do NOT propose extension implementation or packaging steps.
   - Do NOT dispatch other agents.
   - Wait for explicit human approval of dependency changes.

---

## Example of Correct Post-Agent Output
```
⏺ The dxt-dependency-bundler has completed its task.

- Created deps/dxt/file-tools/python/ with virtual environment
- Bundled requests, pathlib, and typing-extensions with version locking
- Optimized bundle size and cross-platform compatibility
- Logged pending dependency bundling decision in docs/decisions.md

Please review the dependency bundle and provide explicit approval.
```

---

## Storage & Usage
- Import into Claude Code with:
  ```
  # Additional Instructions
  - dxt dependency bundler rules @agents-rules/dxt-dependency-bundler-rules.md
  ```