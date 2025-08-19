# Claude Code Subagent Framework

A production-ready framework for implementing strict role-based separation of concerns in software development using Claude Code's Task tool system. This framework ensures disciplined development practices through specialized agents, mandatory approval workflows, and comprehensive state management.

## Overview

This framework implements **12 specialized agents** that handle different aspects of software development:

- **Orchestration**: Project coordination and approval workflows
- **Strategy & Planning**: Vision, requirements, and roadmap management  
- **Technical Implementation**: Contracts, code, tests, and environment setup
- **Quality & Documentation**: Consistency auditing and documentation maintenance

Each agent operates within strict boundaries, requires human approval for changes, and maintains complete audit trails through decision logging.

## Core Principles

### 🔒 Strict Role Separation
- Only specific agents can edit specific file types
- Clear boundaries prevent scope creep and maintain system integrity
- Each agent has explicit constraints and forbidden actions

### 🚦 Mandatory Approval Gates
- All changes require entries in `docs/decisions.md` with `Pending` status
- Agents **STOP** after logging decisions and wait for explicit human approval
- No agent can bypass the approval workflow

### 📊 State-Driven Architecture
- Central state management in `.claude/state.json` (orchestrator-only)
- Complete decision audit trail with ISO 8601 timestamps
- Full traceability of all project changes and approvals

### 🎯 Evidence-Based Operations
- All agents operate on approved specifications and contracts
- Changes must reference existing approved documents
- No assumptions or improvisation allowed

## Agent Architecture

### Orchestration Layer
- **`project-orchestrator`**: State management, approval workflow enforcement, agent dispatch
- **`consistency-auditor`**: Read-only drift detection across contracts, code, and docs

### Strategy & Planning  
- **`product-vision-manager`**: Vision, mission, values, and non-goals (strategy level)
- **`product-requirements-manager`**: Functional requirements definition (what to build)
- **`high-level-project-manager`**: Phases and milestones (when to sequence)
- **`low-level-project-manager`**: Task breakdowns and work structure (how to organize)

### Technical Implementation
- **`data-contracts-manager`**: Versioned JSON Schema contracts with validation rules
- **`python-pro-software-engineer`**: Production code implementation in `src/` only
- **`test-writer`**: Automated test creation and maintenance in `tests/` only
- **`dev-environment-maintainer`**: Dependencies, `pyproject.toml`, and dev setup

### Quality & Documentation
- **`documentation-maintainer`**: Formatting, cross-references, and structural consistency
- **`improvements-manager`**: Enhancement detection and optimization proposals

## Quick Start

### 1. Initialize Your Project

Use Claude Code's Task tool to start with the orchestrator:

```
Task: "Initialize project state for [your project description]"
Agent: project-orchestrator
```

This creates `.claude/state.json` and `docs/decisions.md` with the first pending decision.

### 2. Import Agent Rules

Add the agent rules to your Claude Code session:

```
# Additional Instructions
- orchestration rules @agents-rules/project-orchestrator-rules.md
- data contracts manager rules @agents-rules/data-contracts-manager-rules.md
- python pro software engineer rules @agents-rules/python-pro-software-engineer-rules.md
# ... (add others as needed)
```

### 3. Follow the Workflow

The typical development flow:

1. **Vision & Requirements** → `product-vision-manager` → `product-requirements-manager`
2. **Planning** → `high-level-project-manager` → `low-level-project-manager`  
3. **Implementation** → `data-contracts-manager` → `python-pro-software-engineer` → `test-writer`
4. **Quality** → `consistency-auditor` → `improvements-manager`

**Important**: Each step requires explicit human approval before proceeding.

## File Structure

```
.claude/
  state.json                    # Central project state (orchestrator-only)

docs/
  decisions.md                  # Decision log with approval workflow
  vision.md                     # Product vision and strategy
  requirements.md               # Functional requirements
  roadmap.md                    # High-level project phases  
  backlog.md                    # Detailed task breakdown
  contracts.md                  # Contract version index
  development.md                # Development environment setup
  consistency-report.md         # Drift detection results
  improvement-report.md         # Enhancement proposals

contracts/
  *.v1.json                     # Versioned JSON Schema contracts
  *.v2.json                     # Contract evolution with semver

src/                            # Production Python modules
tests/                          # Automated test suite

agents/                         # Agent definitions (12 files)
agents-rules/                   # Agent behavior rules (12 files)
```

## Agent Usage Examples

### Define Requirements
```
Task: "Create requirements for Excel order processing system"
Agent: product-requirements-manager
```

### Create Data Contracts  
```
Task: "Define JSON schema for normalized order objects v1"
Agent: data-contracts-manager
```

### Implement Code
```
Task: "Implement Excel parser per approved contract v1"  
Agent: python-pro-software-engineer
```

### Write Tests
```
Task: "Create tests for src/excel_parser/"
Agent: test-writer  
```

### Environment Setup
```
Task: "Add pandas and openpyxl dependencies"
Agent: dev-environment-maintainer
```

### Quality Audits
```
Task: "Check for drift between contracts and implementation"
Agent: consistency-auditor
```

## Key Constraints

### Never Bypass Approval Gates
- **All agents STOP** after logging pending decisions
- **No agent proposes next steps** - only humans decide workflow progression
- **Changes are invalid** until explicitly approved by humans

### Respect File Boundaries
- `.claude/state.json` → **only** `project-orchestrator`
- `src/**` → **only** `python-pro-software-engineer`  
- `tests/**` → **only** `test-writer`
- `contracts/**` → **only** `data-contracts-manager`
- `pyproject.toml` → **only** `dev-environment-maintainer`

### Maintain Evidence Chain
- All changes must reference approved specifications
- No improvisation or assumption-making allowed
- Complete audit trail through decision logging
- Traceability from requirements through implementation

## Decision Workflow

Every agent follows this pattern:

1. **Execute Task** within strict role boundaries
2. **Log Decision** in `docs/decisions.md` with status `Pending`  
3. **STOP** immediately and await human input
4. **Human Reviews** and provides explicit approval/rejection
5. **State Updated** with approval timestamp by orchestrator

## Development Best Practices

### Use Appropriate Tools
- **Always use `uv` instead of `pip`** for Python dependencies
- **Prefer Task tool dispatch** over direct file editing
- **Follow agent boundaries** - never bypass role separation
- **Document all decisions** with clear rationale

### Maintain Quality Gates
- **Profile data before transforming** (when applicable)
- **Validate against contracts** before implementation  
- **Run consistency audits** regularly
- **Review improvement suggestions** periodically

### Plan Incrementally  
- **Process requirements in chunks** (1-2 major features per cycle)
- **Version contracts semantically** (breaking vs non-breaking changes)
- **Implement minimal viable solutions** that satisfy contracts
- **Test continuously** against approved specifications

## Troubleshooting

### Agent Won't Proceed
- **Check pending decisions** in `docs/decisions.md`  
- **Provide explicit approval** before expecting progress
- **Use orchestrator** to manage state transitions

### Boundary Violations
- **Review agent constraints** in `agents-rules/*.md`
- **Use correct agent** for specific file types
- **Never edit files directly** - always use appropriate agents

### Workflow Confusion
- **Start with orchestrator** for project initialization  
- **Follow approval gates** - each step requires human input
- **Reference decision log** to understand current project state

## Contributing

This framework is designed for:

- **Software development teams** requiring disciplined practices
- **Compliance-critical projects** needing full audit trails
- **Complex systems** benefiting from role separation  
- **Quality-focused workflows** with evidence-based decisions

The framework enforces best practices through tooling rather than relying on human discipline alone.

## License

This framework is provided as-is for software development process improvement. Adapt the agents and rules to fit your specific project needs while maintaining the core principles of role separation, approval gates, and evidence-based operations.

---

**⚠️ Important**: This framework works exclusively with Claude Code's Task tool system. It requires understanding of agent dispatch patterns and approval workflow management for effective use.