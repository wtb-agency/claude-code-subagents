# Claude Code Subagent Framework

A production-ready framework for implementing strict role-based separation of concerns in software development using Claude Code's Task tool system. This framework ensures disciplined development practices through specialized agents, mandatory approval workflows, and comprehensive state management.

## Overview

This framework implements **24 specialized agents** that handle different aspects of software development:

- **Orchestration**: Project coordination and approval workflows
- **Strategy & Planning**: Vision, requirements, and roadmap management  
- **Technical Implementation**: Contracts, code, tests, and environment setup
- **MCP Server Development**: Protocol compliance, server implementation, tools, resources, client integration, and testing
- **DXT Extension Development**: Claude Desktop extension manifests, configuration, implementation, dependency bundling, packaging, and installation testing
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

### MCP Server Development
- **`mcp-protocol-manager`**: MCP specification compliance and protocol schema contracts
- **`mcp-server-engineer`**: Core MCP server runtime implementation
- **`mcp-tools-manager`**: MCP tool schemas and execution handlers
- **`mcp-resources-manager`**: MCP resource schemas and provider implementations
- **`mcp-client-integration-manager`**: Client configurations and integration guides
- **`mcp-test-engineer`**: MCP-specific testing and protocol validation

### DXT Extension Development
- **`dxt-manifest-manager`**: Claude Desktop extension manifest.json files and metadata
- **`dxt-config-manager`**: User configuration schemas and secure data handling patterns
- **`dxt-extension-engineer`**: Claude Desktop extension implementation and MCP integration
- **`dxt-dependency-bundler`**: Python venv/lib, Node.js node_modules, and binary dependency bundling
- **`dxt-packager`**: .dxt package creation using official DXT toolchain
- **`dxt-installer-tester`**: Installation testing and Claude Desktop integration validation

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
- mcp protocol manager rules @agents-rules/mcp-protocol-manager-rules.md
- mcp server engineer rules @agents-rules/mcp-server-engineer-rules.md
- mcp tools manager rules @agents-rules/mcp-tools-manager-rules.md
- dxt manifest manager rules @agents-rules/dxt-manifest-manager-rules.md
- dxt extension engineer rules @agents-rules/dxt-extension-engineer-rules.md
- dxt packager rules @agents-rules/dxt-packager-rules.md
# ... (add others as needed)
```

### 3. Follow the Workflow

The typical development flow:

1. **Vision & Requirements** → `product-vision-manager` → `product-requirements-manager`
2. **Planning** → `high-level-project-manager` → `low-level-project-manager`  
3. **Implementation** → `data-contracts-manager` → `python-pro-software-engineer` → `test-writer`
4. **MCP Development** → `mcp-protocol-manager` → `mcp-server-engineer` → `mcp-tools-manager` → `mcp-test-engineer`
5. **DXT Extensions** → `dxt-manifest-manager` → `dxt-extension-engineer` → `dxt-dependency-bundler` → `dxt-packager` → `dxt-installer-tester`
6. **Quality** → `consistency-auditor` → `improvements-manager`

**Important**: Each step requires explicit human approval before proceeding.

## File Structure

```
.claude/
  agents/                       # Agent definitions (24 files)
  state.json                    # Central project state (orchestrator-only)

docs/
  decisions.md                  # Decision log with approval workflow
  vision.md                     # Product vision and strategy
  requirements.md               # Functional requirements
  roadmap.md                    # High-level project phases  
  backlog.md                    # Detailed task breakdown
  contracts.md                  # Contract version index
  mcp-contracts.md              # MCP protocol version index
  mcp-integration/              # MCP client integration guides
  dxt-manifests.md              # DXT extension manifest index
  development.md                # Development environment setup
  consistency-report.md         # Drift detection results
  improvement-report.md         # Enhancement proposals

contracts/
  *.v1.json                     # Versioned JSON Schema contracts
  *.v2.json                     # Contract evolution with semver
  mcp/                          # MCP protocol and capability contracts
    tools/                      # MCP tool schemas
    resources/                  # MCP resource schemas
  dxt/                          # DXT extension contracts
    config/                     # Configuration schemas and security patterns

configs/
  mcp-clients/                  # Client-specific MCP configurations
  dxt/                          # DXT extension manifests and metadata

deps/
  dxt/                          # DXT extension dependency bundles
    <extension-name>/           # Per-extension dependency isolation
      python/                   # Python virtual environments
      nodejs/                   # Node.js node_modules bundles
      binaries/                 # Platform-specific binary dependencies

dist/
  dxt/                          # Generated .dxt extension packages

src/                            # Production Python modules
  mcp/                          # MCP server implementation
    server/                     # Core server runtime
    tools/                      # Tool handlers
    resources/                  # Resource providers
  dxt/                          # DXT extension implementations
    <extension-name>/           # Per-extension code isolation

tests/                          # Automated test suite
  mcp/                          # MCP-specific tests
  dxt/                          # DXT extension tests
    installation/               # Installation process testing
    integration/                # Claude Desktop integration tests

agents-rules/                   # Agent behavior rules (24 files)
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

### MCP Development
```
Task: "Define MCP tools capability schema v1"
Agent: mcp-protocol-manager
```

```
Task: "Implement MCP server with stdio transport"
Agent: mcp-server-engineer
```

```
Task: "Create file system tools for MCP server"
Agent: mcp-tools-manager
```

```
Task: "Add Claude Desktop integration config"
Agent: mcp-client-integration-manager
```

```
Task: "Write MCP protocol compliance tests"
Agent: mcp-test-engineer
```

### DXT Extension Development
```
Task: "Create manifest.json for file-tools extension v1"
Agent: dxt-manifest-manager
```

```
Task: "Define secure API key configuration schema"
Agent: dxt-config-manager
```

```
Task: "Implement file-tools extension with MCP integration"
Agent: dxt-extension-engineer
```

```
Task: "Bundle Python dependencies for data-analyzer extension"
Agent: dxt-dependency-bundler
```

```
Task: "Package weather-extension v2.1.0 as .dxt file"
Agent: dxt-packager
```

```
Task: "Test file-tools.dxt installation on macOS and Windows"
Agent: dxt-installer-tester
```

## Key Constraints

### Never Bypass Approval Gates
- **All agents STOP** after logging pending decisions
- **No agent proposes next steps** - only humans decide workflow progression
- **Changes are invalid** until explicitly approved by humans

### Respect File Boundaries
- `.claude/state.json` → **only** `project-orchestrator`
- `src/**` → **only** `python-pro-software-engineer`  
- `src/mcp/**` → **only** `mcp-server-engineer`, `mcp-tools-manager`, `mcp-resources-manager`
- `src/dxt/**` → **only** `dxt-extension-engineer`
- `tests/**` → **only** `test-writer`
- `tests/mcp/**` → **only** `mcp-test-engineer`
- `tests/dxt/**` → **only** `dxt-installer-tester`
- `contracts/**` → **only** `data-contracts-manager`
- `contracts/mcp/**` → **only** `mcp-protocol-manager`, `mcp-tools-manager`, `mcp-resources-manager`
- `contracts/dxt/config/**` → **only** `dxt-config-manager`
- `configs/mcp-clients/**` → **only** `mcp-client-integration-manager`
- `configs/dxt/**` → **only** `dxt-manifest-manager`
- `deps/dxt/**` → **only** `dxt-dependency-bundler`
- `dist/dxt/**` → **only** `dxt-packager`
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