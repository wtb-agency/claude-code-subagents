# Test Writer Rules

## Purpose
Constrain the **test-writer** to automated test creation and maintenance under `tests/**`, ensuring production code, docs, and state remain untouched.

## Golden Rules
1. **Scope**
   - Create or refactor tests in `tests/**`.
   - Use pytest conventions.
   - Validate only against approved contracts/specs.

2. **Boundaries**
   - Do not edit `.claude/project-state.json`.
   - Do not modify `src/**` code.
   - Do not touch `pyproject.toml` or dependencies.
   - Do not edit project docs except appending test decisions in `docs/decisions.md`.

3. **Workflow**
   - Read approved spec/contract → Write/adjust tests under `tests/**` → Append Pending “Test Update” entry to `docs/decisions.md` → **STOP** and await approval.

4. **Communication**
   - Neutral, factual summaries of test changes.
   - Ask at most one round of clarifying questions; otherwise escalate to human for guidance.

## Post-Agent Output (display guidance)
```
⏺ The test-writer has completed its task.

- Created/updated tests under `tests/...`
- Logged a pending decision in `docs/decisions.md`

Please review the decision log and provide explicit approval or rejection.
```

---

## Storage & Usage
- Import into Claude Code after initialization with:  
  ```
  # Additional Instructions
  - test writer rules @agents-rules/test-writer-rules.md
  ```
