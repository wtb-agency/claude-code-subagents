# Orchestration Rules

## Purpose
These rules govern how Claude Code should behave when the **project-orchestrator** agent runs. They ensure strict separation of concerns and prevent Claude Code from embedding unintended suggestions when returning control.

---

## Golden Rules

1. **No Next Step Proposals**
   - After the **project-orchestrator** agent completes, Claude Code must NOT propose next steps, recommendations, or plans.
   - Output should only summarize what the orchestrator did and point the human to the updated `docs/decisions.md` for review.

2. **Human Control**
   - Only the human decides what comes next.
   - Claude Code must explicitly stop and wait for approval before progressing.

3. **Neutral Reporting**
   - Summaries must be factual and neutral.
   - No interpretation, extrapolation, or speculation about what should happen.

---

## Expected Workflow

1. **Run Orchestrator**  
   - Orchestrator edits `.claude/state.json` and `docs/decisions.md`.  

2. **Claude Code Output**  
   - Responds with:  
     - Confirmation of orchestrator completion  
     - A neutral list of what changed (e.g., state updated, decision logged)  
     - A reminder that human approval is required  

3. **Stop**  
   - Do NOT propose “next actions” or “next steps.”  
   - Do NOT dispatch other agents.  
   - Wait silently until human provides explicit instructions.  

---

## Example of Correct Post-Orchestrator Output
```
⏺ The project-orchestrator has completed its task.

- Updated .claude/state.json with initialization data
- Logged a pending decision in docs/decisions.md

Please review the decision log and provide explicit approval or rejection.
```

---

## Storage & Usage
- Import into Claude Code after initialization with:  
  ```
  # Additional Instructions
  - orchestration rules @agents-rules/project-orchestrator-rules.md
  ```
