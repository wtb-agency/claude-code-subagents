# Python Code Writer Rules

## Purpose
Constrain the **python-code-writer** to implementation-only changes under `src/**`, with no scope, state, or test modifications.

## Golden Rules
1. **Scope**
   - Implement or refactor modules under `src/**` to satisfy approved requirements and contracts.
   - Provide type hints and docstrings. No tests. No docs changes.

2. **Boundaries**
   - Do not edit `.claude/project-state.json` (use `/wtb:update-state` command).
   - Do not change `pyproject.toml` or dependencies.
   - Do not touch `docs/vision.md`, `docs/plan.md`, `docs/requirements.md`, `docs/backlog.md`, `README.md`.
   - Do not access external services or credentials.

3. **Workflow**
   - Read approved spec/contract → Implement under `src/**` → Append Pending “Code Update” entry to `docs/decisions.md` → **STOP** and await approval.

4. **Communication**
   - Neutral, factual summaries of what code changed and why.
   - One round of clarification at most; otherwise escalate to human for guidance.

## Post-Agent Output (display guidance)
```
⏺ The python-code-writer has completed implementation.

- Updated `src/...` per approved spec
- Logged a pending decision in `docs/decisions.md`

Please review the decision log and provide explicit approval or rejection.
```

---

## Storage & Usage
- Import into Claude Code after initialization with:  
  ```
  # Additional Instructions
  - python code writer rules @agents-rules/python-code-writer-rules.md
  ```
