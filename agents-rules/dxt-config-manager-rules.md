# DXT Config Manager Rules

## Purpose
These rules govern how Claude Code should behave when the **dxt-config-manager** agent runs. They ensure strict separation between configuration schema definition and other DXT concerns.

---

## Golden Rules

1. **Configuration Schema Only**
   - After the **dxt-config-manager** agent completes, Claude Code must NOT suggest manifest creation, extension implementation, or packaging approaches.
   - Output should only confirm configuration schema creation and security pattern definition.

2. **No Implementation Expansion**
   - Never propose extension code that uses the configuration.
   - Never suggest manifest integration or dependency bundling.

3. **Configuration Boundaries**
   - Only modify files in `contracts/dxt/config/` directory.
   - Never modify manifest files, extension code, or packaging files.

---

## Expected Workflow

1. **Run DXT Config Manager**
   - Agent creates/updates configuration schemas in `contracts/dxt/config/`.
   - Agent logs pending decision in `docs/decisions.md`.

2. **Claude Code Output**
   - Responds with:
     - Confirmation of configuration schema completion
     - List of configuration contracts created/modified
     - Reference to pending decision requiring approval

3. **Stop**
   - Do NOT propose manifest updates or extension implementation.
   - Do NOT dispatch other agents.
   - Wait for explicit human approval of configuration changes.

---

## Example of Correct Post-Agent Output
```
⏺ The dxt-config-manager has completed its task.

- Created contracts/dxt/config/api_keys.v1.json configuration schema
- Defined keychain integration patterns for sensitive data
- Updated security validation rules in contracts/dxt/config/security/
- Logged pending configuration decision in docs/decisions.md

Please review the configuration schema and provide explicit approval.
```

---

## Storage & Usage
- Import into Claude Code with:
  ```
  # Additional Instructions
  - dxt config manager rules @agents-rules/dxt-config-manager-rules.md
  ```