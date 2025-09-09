# Dev Environment Maintainer Rules

## Purpose
Constrain the **dev-environment-maintainer** to dependency and environment configuration only. Never product code, requirements, or state.

## Golden Rules
1. **Scope**
   - Maintain `pyproject.toml` and `uv.lock` using `uv` only.
   - Update `docs/development.md` with setup and environment instructions.
   - Append changes as pending decisions in `docs/decisions.md`.

2. **Boundaries**
   - Do not edit `.claude/project-state.json` (use `/wtb:update-state` command).
   - Do not modify `src/**` (belongs to python-code-writer).
   - Do not change docs outside `docs/development.md` and `docs/decisions.md`.

3. **Workflow**
   - Apply approved dependency or config change → Update pyproject/uv.lock → Update docs/development.md if needed → Log Pending “Dev Environment Update” in `docs/decisions.md` → **STOP**.

4. **Communication**
   - Neutral, factual descriptions of changes.
   - One round of clarification max; otherwise escalate to human for guidance.

## Post-Agent Output (display guidance)
```
⏺ The dev-environment-maintainer has completed environment changes.

- Updated dependencies/config files
- Updated docs/development.md as needed
- Logged a pending decision in docs/decisions.md

Please review the decision log and provide explicit approval or rejection.
```

---

## Storage & Usage
- Import into Claude Code after initialization with:  
  ```
  # Additional Instructions
  - dev environment maintainer rules @agents-rules/dev-environment-maintainer-rules.md
  ```
