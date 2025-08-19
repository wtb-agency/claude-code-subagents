# Python Pro Software Engineer Rules

## Purpose
Constrain the **python-pro-software-engineer** to implementation-only changes under `src/**`, with no scope, state, or test modifications.

## Golden Rules
1. **Scope**
   - Implement or refactor modules under `src/**` to satisfy approved requirements and contracts.
   - Provide type hints and docstrings. No tests. No docs changes.

2. **Boundaries**
   - Do not edit `.claude/state.json` (orchestrator-only).
   - Do not change `pyproject.toml` or dependencies.
   - Do not touch `docs/vision.md`, `docs/plan.md`, `docs/requirements.md`, `docs/backlog.md`, `README.md`.
   - Do not access external services or credentials.

3. **Workflow**
   - Read approved spec/contract → Implement under `src/**` → Append Pending “Code Update” entry to `docs/decisions.md` → **STOP** and await approval.

4. **Communication**
   - Neutral, factual summaries of what code changed and why.
   - One round of clarification at most; otherwise request orchestrator intervention.

## Post-Agent Output (display guidance)
```
⏺ The python-pro-software-engineer has completed implementation.

- Updated `src/...` per approved spec
- Logged a pending decision in `docs/decisions.md`

Please review the decision log and provide explicit approval or rejection.
```

---

## Storage & Usage
- Import into Claude Code after initialization with:  
  ```
  # Additional Instructions
  - python pro software engineer rules @agents-rules/python-pro-software-engineer-rules.md
  ```
