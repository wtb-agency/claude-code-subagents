# Claude Code Subagent Library

A library of 24 specialized agents for Claude Code's Task tool, plus a bootstrap system to create focused customer projects. Includes agent coordination framework with file ownership boundaries and approval workflows.

## Overview

This library provides **24 specialized agents** and a **project bootstrap system** for different aspects of software development:

- **Orchestration**: Project coordination and approval workflows
- **Strategy & Planning**: Vision, requirements, and roadmap management  
- **Technical Implementation**: Contracts, code, tests, and environment setup
- **MCP Server Development**: Protocol compliance, server implementation, tools, resources, client integration, and testing
- **DXT Extension Development**: Claude Desktop extension manifests, configuration, implementation, dependency bundling, packaging, and installation testing
- **Quality & Documentation**: Consistency auditing and documentation maintenance

Agents get copied to customer projects where they work within file ownership boundaries and approval workflows.

## How the Bootstrap System Works

### Project Bootstrap Process
1. **Conversation**: `project-bootstrap` agent gathers project requirements
2. **Analysis**: Determines which agents are needed based on project type
3. **Selection**: Copies only relevant agents from the library (not all 24)
4. **Setup**: Creates project structure, runs `uv init` for Python projects
5. **Documentation**: Generates project-specific README.md with usage instructions

### Agent Selection Examples
- **DXT Extension**: Copies orchestrator + DXT agents (10 agents)
- **MCP Server**: Copies orchestrator + MCP agents (8 agents) 
- **Data Pipeline**: Copies orchestrator + code/test agents (6 agents)
- **Full Project**: Copies orchestrator + planning + implementation agents (12+ agents)

### Bootstrapped Project Structure
Each customer project gets:
- Selected agents copied to `.claude/agents/`
- Corresponding rules copied to `agents-rules/`
- `AGENTS.md` coordination framework
- Project-specific README.md with usage examples

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
- **`python-code-writer`**: Code implementation in `src/` only
- **`test-writer`**: Automated test creation and maintenance in `tests/` only
- **`dev-environment-maintainer`**: Dependencies, `pyproject.toml`, and dev setup

### MCP Server Development
- **`mcp-schema-writer`**: MCP protocol schema files
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
- **`dxt-zip-creator`**: .dxt package creation (zip files)
- **`dxt-installer-tester`**: Installation testing and Claude Desktop integration validation

### Quality & Documentation
- **`documentation-maintainer`**: Formatting, cross-references, and structural consistency
- **`improvements-manager`**: Enhancement detection and optimization proposals

## Bootstrap New Projects

Use the bootstrap agent to create focused customer projects:

**Bootstrap Usage:**
```bash
# Create DXT extension project
Task: "Bootstrap new DXT extension for Shopify order validation" 
Agent: project-bootstrap

# Create MCP server project
Task: "Bootstrap MCP server for file system operations"
Agent: project-bootstrap

# Create data pipeline project  
Task: "Bootstrap Python project for CSV processing"
Agent: project-bootstrap
```

**The bootstrap agent will gather requirements, select appropriate agents, and create a focused project setup.**

## Library File Structure

```
claude-code-subagents/                  # This repository
├── .claude/
│   └── agents/
│       └── project-bootstrap.md       # Bootstrap tool
├── agents/                            # Agent library (24 agents)
│   ├── project-orchestrator.md
│   ├── consistency-auditor.md  
│   ├── [6 MCP agents...]
│   ├── [6 DXT agents...]
│   └── [remaining agents...]
├── agents-rules/                      # Rules library (24 rules)
│   ├── project-orchestrator-rules.md
│   ├── consistency-auditor-rules.md
│   └── [corresponding rules...]
├── AGENTS.md                          # Coordination framework (copied to projects)
├── README.md                          # This file
└── CLAUDE.md                          # Library usage guide for Claude Code
```

## Contributing

This is an experimental framework for:

- **File coordination** when multiple people work on different parts
- **Basic approval tracking** to avoid unreviewed changes
- **Task dispatch** using Claude Code's agent system

It's a simple coordination system, not an enforcement tool.

## License

This experimental framework is provided as-is. Adapt the agents and rules to fit your project needs.

---

**⚠️ Important**: This framework works exclusively with Claude Code's Task tool system. It requires understanding of agent dispatch patterns and approval workflow management for effective use.