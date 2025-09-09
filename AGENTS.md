# Claude Code Subagent Coordination Rules

Primary scope: These coordination rules are optimized for building Claude Desktop extensions using MCP Bundles (MCPB). The framework also supports MCP server and general code workflows, but the intended main use is MCPB-based Claude Desktop extension development.

**IMPORTANT**: This file defines behavioral rules specifically for Claude Code's **general-purpose agent**. If Claude Code's agent structure changes (e.g., "general-purpose agent" is renamed or replaced), Claude Code must immediately flag this change when processing requests and request updated coordination rules.

This file defines how Claude Code's **general-purpose agent** should behave when working with the specialized subagents in this framework. These rules ensure proper agent coordination, prevent workflow corruption, and maintain the integrity of the state-driven architecture for Claude Desktop extension development via MCP Bundles.

## Core Principles

1. **Native Orchestration**: Claude Code's general-purpose agent uses slash commands and hooks for orchestration
2. **Prompt Validation**: Every agent dispatch must follow exact syntax and be validated by the general-purpose agent before execution
3. **State Protection**: Hooks enforce file ownership boundaries and prevent unauthorized modifications
4. **Approval Gate Enforcement**: Hooks automatically log decisions and require human approval via `/wtb:approve-decision` command
5. **Context7 Integration**: Agents can include "use context7" in queries for real-time documentation access without direct tool calls

## Terminology
- "Claude Desktop extension" → implemented and packaged as an MCP Bundle (`.mcpb`)

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
```

### When Claude Code's General-Purpose Agent Should Use Each Mode
- **Start conversational**: Ask clarifying questions, understand what human wants
- **Switch to structured dispatch**: When task is clear and specialized agent can execute
- **Return to conversational**: After specialized agent completes, discuss results and next steps
- **Stay conversational**: For brainstorming, problem-solving, status updates

## Context7 Integration Patterns

### Enhanced Agent Capabilities
Agents with Context7 integration can access real-time documentation automatically:

**Context7-Enhanced Agents:**
- **`bullshit-detector`**: Include "use context7" in reality-check queries to verify technical claims against current API capabilities
- **`python-code-writer`**: Add "use context7" to coding requests for up-to-date examples and best practices
- **`documentation-maintainer`**: Use "use context7" when updating examples to ensure current API usage

**Example Dispatch with Context7:**
```bash
Task: "Reality-check our MCP Bundle claims about Claude Desktop API capabilities. use context7 to verify current MCPB development limitations and manifest requirements."
Agent: bullshit-detector
```

**Context7 Usage Guidelines:**
- Include "use context7" in agent prompts when current documentation is needed
- Context7 works through natural language, not direct tool calls
- Agents automatically get injected with real-time documentation context
- No special configuration needed - Context7 works globally when configured

## Native Orchestration Commands

### Slash Commands for State Management
Claude Code's general-purpose agent should **suggest these commands** to users for orchestration:

- **`/wtb:init-project`**: Initialize `.claude/project-state.json` and project structure
- **`/wtb:update-state`**: Safely modify project state with validation and logging
- **`/wtb:approve-decision`**: Process pending decisions from approval workflow
- **`/wtb:audit-project`**: Run consistency checks across project files

### Command Suggestion Protocol
- **NEVER execute slash commands directly** - agents cannot run slash commands
- **Always suggest commands** with clear explanation of what they do
- **Provide exact command syntax** for user to copy/paste
- **Explain the expected outcome** after command execution

### When to Suggest Commands vs Agent Dispatch
- **Suggest slash commands** for state management, approvals, and project setup
- **Use agent dispatch** for specialized work (requirements, code, contracts, etc.)
- **Hooks handle** boundary enforcement and approval logging automatically

### Command Suggestion Examples
Instead of: "I'll use /wtb:update-state to modify the project state"
Say: "Please run this command to update the project state:
```
/wtb:update-state --state-file .claude/project-state.json --key "project.version" --value "2.0.0"
```
This will safely modify .claude/project-state.json with validation and logging."

### Typical Workflow Pattern
```
1. User: "Initialize a new Python project"
2. Agent: "Please run: /wtb:init-project MyProject --type python
   This creates .claude/project-state.json and project structure."
3. User: [Executes /wtb:init-project MyProject --type python]
4. Agent: "Great! Project initialized. Would you like me to create requirements?"
5. User: "Yes, create requirements for order processing"
6. Agent: [Dispatches product-requirements-manager agent]
7. Agent completes requirements → Hooks log pending decision
8. Agent: "Requirements created! Please run: /wtb:approve-decision --latest
   to approve the new requirements before proceeding."
```

## Dispatch Pattern Recognition

### Pattern Matching
Claude Code's general-purpose agent should recognize when agent dispatch requests follow the expected patterns:
- **Standard Pattern**: `Task: "[description]" / Agent: [agent-name]`
- **All agent dispatch is direct** - no handoff coordination needed

### When Patterns Don't Match
If dispatch requests are unclear or malformed:
- **Ask for Clarification**: Request properly formatted dispatch syntax
- **Suggest Corrections**: Recommend appropriate agent for the task
- **Flag Unknown Agents**: Point out if requested agent doesn't exist in the framework
- **Question Scope Mismatches**: If task seems wrong for the requested agent, suggest alternatives

## Agent Boundary Enforcement

### Core Framework Agents
- **python-code-writer**: Only edits `src/**` (excluding MCP/MCPB subdirs)
- **test-writer**: Only edits `tests/**` (excluding MCP/MCPB subdirs)
- **data-contracts-manager**: Only edits `contracts/**` (excluding MCP/MCPB subdirs)
- **dev-environment-maintainer**: Only edits `pyproject.toml`

### Strategy & Planning Agents
- **product-vision-manager**: Only edits `docs/vision.md` (hooks log decisions)
- **product-requirements-manager**: Only edits `docs/requirements.md` (hooks log decisions)
- **high-level-project-manager**: Only edits `docs/roadmap.md` (hooks log decisions)
- **low-level-project-manager**: Only edits `docs/backlog.md` (hooks log decisions)

### Quality & Documentation Agents  
- **consistency-auditor**: Read-only access (no file modifications except reports)
- **documentation-maintainer**: Only edits documentation structure/formatting (no content creation)
- **improvements-manager**: Read-only access (provides recommendations only)
- **bullshit-detector**: Read-only access (reality-check audit reports only)

### MCP Server Agents
- **mcp-schema-writer**: Only edits `contracts/mcp/**` (general protocols)
- **mcp-server-engineer**: Only edits `src/mcp/server/**`
- **mcp-tools-manager**: Only edits `src/mcp/tools/**` and `contracts/mcp/tools/**`
- **mcp-resources-manager**: Only edits `src/mcp/resources/**` and `contracts/mcp/resources/**`
- **mcp-client-integration-manager**: Only edits `configs/mcp-clients/**`
- **mcp-test-engineer**: Only edits `tests/mcp/**`

### MCP Bundle Agents
- **mcpb-extension-manager**: Only edits `server/**` (e.g., `server/main.py`); no manifest or packaging
- **mcpb-packager**: Only edits `*.mcpb` files and `dist/` directory for package creation

### Boundary Violations
Claude Code's general-purpose agent must prevent and flag:
- Agents attempting to edit files outside their ownership
- Multiple agents trying to modify the same files
- Direct file edits that bypass agent coordination
- State changes without proper approval workflows

## Post-Agent Behavior Rules

### After User Runs Suggested Slash Commands
Claude Code's general-purpose agent must:
- **Acknowledge** that user ran the command successfully
- **Summarize** what the command accomplished
- **Point to** relevant files or pending decisions created
- **Ask** if user wants to proceed with next steps

### After Specialized Agents Complete
Claude Code's general-purpose agent must:
- **Confirm** task completion and file changes
- **Reference** pending decisions (hooks handle logging automatically)
- **Stop** and wait for human approval via `/wtb:approve-decision` before proceeding
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
3. **Reference Rules in Dispatch**: When dispatching agents, mention any rule constraints that affect the task

### Rules File Structure
Agent rules files follow the pattern:
- `agents-rules/product-requirements-manager-rules.md` 
- `agents-rules/data-contracts-manager-rules.md`
- `agents-rules/python-code-writer-rules.md`
- `agents-rules/consistency-auditor-rules.md`
- `agents-rules/[agent-name]-rules.md`

### Practical Rules Integration
When dispatching an agent, Claude Code's general-purpose agent should:

1. **Read Agent Rules**: Manually read `agents-rules/[agent-name]-rules.md` if available
2. **Include Constraints**: Add relevant rule constraints to the task description
3. **Flag Conflicts**: If task obviously conflicts with agent scope, suggest alternatives
4. **Document Context**: Include rule context in dispatch reasoning

### Example Rules Integration
```bash
# Instead of complex enforcement, do this:
Task: "Create consistency report for project state per consistency-auditor-rules.md constraints: audit-only mode, no fixes, generate report and decision log entry only"
Agent: consistency-auditor

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
2. **Direct Dispatch**: Test agent dispatch works without handoff coordination
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

**Command Suggestions (agent suggests, user executes):**
```
Agent: "To initialize your project, please run:
/wtb:init-project MyProject --type python

This will create .claude/project-state.json and set up the project structure."

Agent: "To approve the pending decision, please run:
/wtb:approve-decision --latest

This will mark the latest pending decision as approved."
```

**Direct Agent Dispatch (no handoffs needed):**
```bash
Task: "Create requirements for order validation workflow"
Agent: product-requirements-manager

Task: "Create MCPB manifest for file validator bundle"
Agent: mcpb-manifest-builder

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

# Invalid - agent trying to execute slash commands
Agent: "I'll run /wtb:init-project to set up the project"

# Invalid - handoff patterns are not supported
Task: "Create contracts"
Agent: data-contracts-manager
Handoff: project-orchestrator
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
