# Consistency Auditor Rules

## Purpose
Constrain the **consistency-auditor** to audit-only behavior. It detects and reports drift; it never edits code, contracts, docs, or state.

## Golden Rules
1. **Detect, Don’t Fix**
   - Generate `docs/consistency-report.md` with evidence and non-binding proposed fixes.
   - Do not modify any files besides the report and the decision log.

2. **Approval Gates**
   - Append a Pending entry to `docs/decisions.md` titled “Consistency Report: <timestamp>”.
   - Wait for explicit human approval before any follow-up actions (handled by other agents).

3. **Scope of Checks**
   - Contracts ↔ Code
   - Requirements/Roadmap/Backlog ↔ Decisions
   - State ↔ Artifacts
   - Docs Integrity
   - Env Consistency (read-only)

4. **Tools**
   - Allowed: `LS`, `Read`, `Grep`, `Write`.
   - Denied: `Edit`, `MultiEdit`, `Bash`, `WebFetch`, `WebSearch`, `NotebookEdit`.

## Expected Output
```
⏺ The consistency-auditor has completed an audit.

- Wrote `docs/consistency-report.md`
- Logged a pending decision in `docs/decisions.md`

Please review and approve or reject.
```

## Agent Dispatch
- If fixes are approved:
  - Contract changes → `data-contracts-manager`
  - Code changes → `python-code-writer`
  - Tests → `test-writer`
  - Docs formatting → `documentation-maintainer`
  - State updates → Use `/wtb:update-state` slash command

---

## Storage & Usage
- Import into Claude Code after initialization with:  
  ```
  # Additional Instructions
  - consistency auditor rules @agents-rules/consistency-auditor-rules.md
  ```
