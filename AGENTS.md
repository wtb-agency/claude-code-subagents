# Claude Code Subagent Coordination Rules

**IMPORTANT**: This file defines behavioral rules specifically for Claude Code's **general-purpose agent**. If Claude Code's agent structure changes (e.g., "general-purpose agent" is renamed or replaced), Claude Code must immediately flag this change when processing requests and request updated coordination rules.

This file defines how Claude Code's **general-purpose agent** should behave when working with the specialized subagents in this framework. These rules ensure proper agent coordination, prevent workflow corruption, and maintain the integrity of the state-driven architecture.

## Core Principles

1. **Intelligent Handoff Determination**: Claude Code's general-purpose agent analyzes each task and auto-determines when coordination with project-orchestrator is required
2. **Prompt Validation**: Every agent dispatch must follow exact syntax and be validated by the general-purpose agent before execution
3. **State Protection**: Only authorized specialized agents can modify critical project files
4. **Approval Gate Enforcement**: All state-changing operations require human approval before proceeding

## How Claude Code's General-Purpose Agent Should Work

### Conversational Style
Claude Code's general-purpose agent should maintain **casual, conversational interaction** with humans for:
- Questions about project direction
- Clarifications on requirements  
- Discussion of options and trade-offs
- Status updates and progress reports

### Structured Agent Dispatch
When dispatching agents, Claude Code's general-purpose agent should **write proper structured prompts** that include:
- Clear task description and scope
- Specific deliverables expected
- Boundaries and constraints
- Approval requirements

### Two-Mode Operation
**Conversation Mode**: Casual discussion with human
```
"I see you want to create requirements. Let me clarify a few things:
- What's the main functionality you're building?
- Do you want detailed technical specs or high-level workflow?
- Are there specific integrations or security requirements?"
```

**Agent Dispatch Mode**: Structured prompt for task tool
```bash
Task: "Create functional requirements for [project name] focusing on [key functionality], human approval gates, and [specific technical needs]. Include acceptance criteria for each requirement."
Agent: product-requirements-manager
Handoff: auto-determine
```

### When Claude Code's General-Purpose Agent Should Use Each Mode
- **Start conversational**: Ask clarifying questions, understand what human wants
- **Switch to structured dispatch**: When task is clear and specialized agent can execute
- **Return to conversational**: After specialized agent completes, discuss results and next steps
- **Stay conversational**: For brainstorming, problem-solving, status updates

## Handoff Decision Logic

### When Handoffs Are Needed
Claude Code's general-purpose agent should handoff to project-orchestrator when tasks involve:
- ✅ Creating/modifying `.claude/state.json` or `docs/decisions.md`
- ✅ Creating project artifacts (requirements, contracts, documentation)
- ✅ Implementation work requiring human approval gates
- ✅ Multi-agent coordination or workflow state changes

### Decision Process
When using `Handoff: auto-determine`, Claude Code should:
1. **Analyze Task**: Read the task description and evaluate state impact
2. **Make Decision**: Determine if project-orchestrator coordination is needed based on the criteria above
3. **Use Task Tool**: Dispatch with appropriate handoff reasoning if coordination is needed
4. **Ask for Clarification**: If handoff requirements are unclear, ask the human

### Common Handoff Reasons
When handoff is needed, Claude Code should use clear reasoning:
- `state management` - Task affects `.claude/state.json`
- `approval gate` - Task creates artifacts requiring human approval
- `artifact creation` - Task creates requirements, contracts, or documentation
- `workflow coordination` - Task requires multi-agent coordination
- `decision tracking` - Task affects `docs/decisions.md` entries

## Dispatch Pattern Recognition

### Pattern Matching
Claude Code's general-purpose agent should recognize when agent dispatch requests follow the expected patterns:
- **Standard Pattern**: `Task: "[description]" / Agent: [agent-name] / Handoff: auto-determine`
- **Explicit Control**: `Task: "[description]" / Agent: [agent-name] / Handoff: project-orchestrator for [reason]`
- **Simple Tasks**: `Task: "[description]" / Agent: [agent-name]`

### When Patterns Don't Match
If dispatch requests are unclear or malformed:
- **Ask for Clarification**: Request properly formatted dispatch syntax
- **Suggest Corrections**: Recommend appropriate agent or handoff approach
- **Flag Unknown Agents**: Point out if requested agent doesn't exist in the framework
- **Question Scope Mismatches**: If task seems wrong for the requested agent, suggest alternatives

## Agent Boundary Enforcement

### Core Framework Agents
- **project-orchestrator**: Only agent that can edit `.claude/state.json`
- **consistency-auditor**: Read-only access (no file modifications except reports)
- **python-code-writer**: Only edits `src/**` (excluding MCP/DXT subdirs)
- **test-writer**: Only edits `tests/**` (excluding MCP/DXT subdirs)
- **data-contracts-manager**: Only edits `contracts/**` (excluding MCP/DXT subdirs)
- **dev-environment-maintainer**: Only edits `pyproject.toml`

### Strategy & Planning Agents
- **product-vision-manager**: Only edits vision/mission/values in `.claude/state.json` (via orchestrator handoff)
- **product-requirements-manager**: Only edits `docs/requirements.md` (via orchestrator handoff)
- **high-level-project-manager**: Only edits `docs/roadmap.md` (via orchestrator handoff)
- **low-level-project-manager**: Only edits `docs/backlog.md` (via orchestrator handoff)

### Quality & Documentation Agents  
- **documentation-maintainer**: Only edits documentation structure/formatting (no content creation)
- **improvements-manager**: Read-only access (provides recommendations only)

### MCP Server Agents
- **mcp-schema-writer**: Only edits `contracts/mcp/**` (general protocols)
- **mcp-server-engineer**: Only edits `src/mcp/server/**`
- **mcp-tools-manager**: Only edits `src/mcp/tools/**` and `contracts/mcp/tools/**`
- **mcp-resources-manager**: Only edits `src/mcp/resources/**` and `contracts/mcp/resources/**`
- **mcp-client-integration-manager**: Only edits `configs/mcp-clients/**`
- **mcp-test-engineer**: Only edits `tests/mcp/**`

### DXT Extension Agents
- **dxt-manifest-manager**: Only edits `configs/dxt/**`
- **dxt-config-manager**: Only edits `contracts/dxt/config/**`
- **dxt-extension-engineer**: Only edits `src/dxt/**`
- **dxt-dependency-bundler**: Only edits `deps/dxt/**`
- **dxt-zip-creator**: Only edits `dist/dxt/**`
- **dxt-installer-tester**: Only edits `tests/dxt/**`

### Boundary Violations
Claude Code's general-purpose agent must prevent and flag:
- Agents attempting to edit files outside their ownership
- Multiple agents trying to modify the same files
- Direct file edits that bypass agent coordination
- State changes without proper approval workflows

## Post-Agent Behavior Rules

### After project-orchestrator Completes
Claude Code's general-purpose agent must:
- **Summarize** what the orchestrator accomplished
- **Point to** `docs/decisions.md` for review
- **NEVER** propose next actions or dispatch other agents
- **Wait** for explicit human instructions

### After Specialized Agents Complete
Claude Code's general-purpose agent must:
- **Confirm** task completion and file changes
- **Reference** pending decisions requiring approval
- **Stop** and wait for human approval before proceeding
- **Never** auto-dispatch follow-up agents

### Never Assume Next Steps
- No automatic agent chaining without explicit human instruction
- No proactive suggestions for implementation steps
- No bypassing of approval gates or human oversight
- Clear separation between task completion and next actions

## Problem Recognition and Human Escalation

### When Things Go Wrong
When agent coordination doesn't work as expected:
1. **Report the Issue**: Clearly describe what went wrong or what's unclear
2. **Ask for Guidance**: Request human clarification on how to proceed  
3. **Suggest Alternatives**: Recommend different dispatch approaches if appropriate
4. **Wait for Instructions**: Don't attempt to fix coordination issues autonomously

### After Agent Completion
Claude Code's general-purpose agent should:
- **Summarize Results**: Briefly describe what the agent accomplished
- **Note Pending Decisions**: Point to `docs/decisions.md` if approval is needed
- **Stop and Wait**: Don't propose next steps or dispatch additional agents
- **Ask for Direction**: Wait for explicit human instruction on how to proceed

### Approval Gate Behavior
- **Recognize Approval Needs**: Identify when tasks create artifacts requiring human review
- **Stop After Agent Completion**: Don't continue workflow until human provides approval
- **Reference Decision Logs**: Point humans to `docs/decisions.md` for pending approvals
- **Wait for Explicit Approval**: Never assume approval or bypass human oversight

## Agent Rules Integration

### Agent-Specific Rules Files
Each customer project includes `agents-rules/*.md` files that define behavioral constraints for individual agents. While Claude Code's general-purpose agent cannot automatically enforce these rules, it should:

1. **Include Rules in Task Prompts**: When dispatching an agent, manually read the corresponding `agents-rules/[agent-name]-rules.md` and include relevant constraints in the task description
2. **Flag Obvious Mismatches**: If a task clearly conflicts with an agent's known scope, suggest a different agent or task modification
3. **Reference Rules in Handoffs**: When coordinating with project-orchestrator, mention any rule constraints that affect the task

### Rules File Structure
Agent rules files follow the pattern:
- `agents-rules/project-orchestrator-rules.md`
- `agents-rules/product-requirements-manager-rules.md` 
- `agents-rules/data-contracts-manager-rules.md`
- `agents-rules/consistency-auditor-rules.md`
- `agents-rules/[agent-name]-rules.md`

### Practical Rules Integration
When dispatching an agent, Claude Code's general-purpose agent should:

1. **Read Agent Rules**: Manually read `agents-rules/[agent-name]-rules.md` if available
2. **Include Constraints**: Add relevant rule constraints to the task description
3. **Flag Conflicts**: If task obviously conflicts with agent scope, suggest alternatives
4. **Document Context**: Include rule context in handoff reasoning

### Example Rules Integration
```bash
# Instead of complex enforcement, do this:
Task: "Create consistency report for project state per consistency-auditor-rules.md constraints: audit-only mode, no fixes, generate report and decision log entry only"
Agent: consistency-auditor
Handoff: auto-determine

# Additional Instructions for the agent:
- consistency auditor rules @agents-rules/consistency-auditor-rules.md
```

### Limitations and Reality
Claude Code's general-purpose agent **CANNOT**:
- Automatically enforce agent rules
- Block agent actions in real-time
- Validate agent behavior during execution
- Prevent rule violations proactively

Claude Code's general-purpose agent **CAN**:
- Read rules files and include constraints in task descriptions
- Flag obvious task/agent mismatches before dispatch
- Include rule references in agent additional instructions
- Stop after agent completion per approval gate requirements

## Testing and Validation

### Pre-Production Checks
Before using these coordination rules in customer projects:
1. **Syntax Validation**: Verify all three dispatch patterns work correctly
2. **Handoff Logic**: Test auto-determination against known scenarios
3. **Boundary Enforcement**: Confirm agent file ownership rules prevent violations
4. **Approval Gates**: Validate that state changes require human approval

### Customer Project Setup
When copying to customer projects:
1. **Copy** this AGENTS.md file to the customer project root
2. **Import** relevant agent definitions from `agents/` directory
3. **Copy** agent-specific rules from `agents-rules/` directory
4. **Validate** that all copied agents have corresponding rules files
5. **Test** coordination with simple agent dispatch before complex workflows
6. **Verify** rules enforcement prevents boundary violations and tool misuse

## Examples

### Correct Usage
```bash
# Auto-determine handoff (recommended)
Task: "Create requirements for order validation workflow"
Agent: product-requirements-manager
Handoff: auto-determine

# Explicit handoff control
Task: "Create DXT manifest for file validator extension"
Agent: dxt-manifest-manager
Handoff: project-orchestrator for approval gate

# Simple audit task
Task: "Review codebase for contract/code drift"
Agent: consistency-auditor
```

### Validation Errors to Flag
```bash
# Invalid syntax - missing agent
Task: "Create some requirements"

# Invalid syntax - unknown agent
Task: "Build the frontend"
Agent: frontend-developer

# Invalid syntax - malformed handoff
Task: "Create contracts"
Agent: data-contracts-manager
Handoff: orchestrator please
```

## Agent Structure Change Detection

**CRITICAL**: If Claude Code's agent structure changes, the general-purpose agent must:
1. **Immediately flag** when processing any request that references "general-purpose agent"
2. **Report** the specific change (e.g., "general-purpose agent" renamed to "coordinator agent")
3. **Request** updated AGENTS.md file with corrected agent references
4. **Refuse** to proceed until coordination rules are updated to match current agent structure

This ensures coordination rules remain synchronized with Claude Code's actual agent architecture.

---

These rules ensure that Claude Code's general-purpose agent maintains proper coordination with specialized subagents while protecting project state integrity and enforcing human approval workflows. They should be imported into each customer project alongside the relevant agent definitions and agent-specific rules.