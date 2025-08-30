# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Claude Code Subagent Library** that provides:
- **Agent Library**: Collection of 24 specialized agents for different development tasks
- **Rules Library**: Behavioral constraints and boundaries for each agent  
- **Bootstrap Tool**: `project-bootstrap` agent that creates new customer projects
- **Coordination Framework**: AGENTS.md file with dispatch patterns for bootstrapped projects

The agents in this library get copied to customer projects where Claude Code's general-purpose agent uses them via the Task tool system.

## Library Structure

### Agent Library (`agents/`)
Contains 24 specialized agents organized by category:
- **Orchestration**: Project coordination and approval workflows
- **Strategy & Planning**: Vision, requirements, and roadmap management  
- **Technical Implementation**: Contracts, code, tests, and environment setup
- **MCP Server Development**: Protocol schemas, server implementation, tools, resources, client integration, and testing
- **DXT Extension Development**: Manifests, configuration, implementation, dependency bundling, packaging, and installation testing
- **Quality & Documentation**: Consistency auditing and documentation maintenance

### Available Agents in Library

**Orchestration (Always Included):**
- `project-orchestrator`: State management and approval workflows
- `consistency-auditor`: Read-only drift detection across project files

**Strategy & Planning:**
- `product-vision-manager`: Vision, mission, values definition
- `product-requirements-manager`: Functional requirements documentation
- `high-level-project-manager`: Project phases and milestones
- `low-level-project-manager`: Task breakdowns and work organization

**Technical Implementation:**
- `data-contracts-manager`: JSON Schema contracts in `contracts/` with versioning
- `python-code-writer`: Code in `src/` only (no tests, no docs)
- `test-writer`: Automated tests in `tests/` only
- `dev-environment-maintainer`: `pyproject.toml`, `uv` dependencies, dev setup

**MCP Server Development:**
- `mcp-schema-writer`: MCP protocol schema files
- `mcp-server-engineer`: MCP server runtime implementation
- `mcp-tools-manager`: MCP tool schemas and handlers
- `mcp-resources-manager`: MCP resource schemas and providers
- `mcp-client-integration-manager`: Client configurations and integration guides
- `mcp-test-engineer`: MCP-specific testing

**DXT Extension Development:**
- `dxt-manifest-manager`: Claude Desktop extension manifest.json files and metadata
- `dxt-config-manager`: User configuration schemas and secure data handling patterns
- `dxt-extension-engineer`: Claude Desktop extension implementation and MCP integration
- `dxt-dependency-bundler`: Python venv/lib, Node.js node_modules, and binary dependency bundling
- `dxt-zip-creator`: .dxt package creation (zip files)
- `dxt-installer-tester`: Installation testing and Claude Desktop integration validation

**Quality & Documentation:**
- `documentation-maintainer`: Formatting, cross-references, consistency (no content creation)
- `improvements-manager`: Enhancement suggestions and optimization recommendations

## Using This Library

### Bootstrap New Projects
Use the `project-bootstrap` agent in this repo to create new customer projects:

```bash
Task: "Bootstrap new project for [description]"
Agent: project-bootstrap
```

The bootstrap agent will:
1. **Gather requirements** conversationally 
2. **Select appropriate agents** from the library based on project type
3. **Copy selected agents + rules** to new project location
4. **Generate project-specific README.md** with usage instructions
5. **Set up project structure** with `uv init` for Python projects

### Library Maintenance
When working in this library repo:
- **Agents library**: Update agent definitions in `agents/`
- **Rules library**: Update behavioral rules in `agents-rules/`
- **Bootstrap tool**: Modify `.claude/agents/project-bootstrap.md`
- **Coordination framework**: Update `AGENTS.md` with dispatch patterns

### Example Bootstrap Usage
```bash
# Create DXT extension project
Task: "Bootstrap new DXT extension for Shopify order validation"
Agent: project-bootstrap

# Create MCP server project  
Task: "Bootstrap MCP server for file system operations"
Agent: project-bootstrap

# Create data pipeline project
Task: "Bootstrap Python project for CSV data processing"
Agent: project-bootstrap
```

## Development Commands

This is a library repository. The main command is using the bootstrap agent to create new customer projects.

## Library File Structure

```
claude-code-subagents/                  # Agent library repository
├── .claude/
│   └── agents/
│       └── project-bootstrap.md       # Bootstrap tool for new projects
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
├── AGENTS.md                          # Coordination framework for bootstrapped projects
├── README.md                          # Library documentation
└── CLAUDE.md                          # This file - library usage guide
```

## Notes

This is an **agent library repository**. The agents here get copied to customer projects where Claude Code's general-purpose agent uses them with the Task tool system.

For bootstrapped project usage patterns, see the `AGENTS.md` file which gets copied to each customer project.
