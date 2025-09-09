# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Claude Code Subagent Library** primarily aimed at developing Claude Desktop extensions as MCP Bundles (MCPB). It also supports MCP server and general Python workflows. It provides:
- **Agent Library**: Collection of 21 specialized agents for different development tasks
- **WTB Command Library**: 8 focused WTB commands for MCP/MCPB workflows
- **Rules Library**: Behavioral constraints and boundaries for each agent  
- **Bootstrap Tool**: `project-bootstrap` agent that creates new customer projects
- **Coordination Framework**: AGENTS.md file with dispatch patterns for bootstrapped projects

The agents in this library get copied to customer projects where Claude Code's general-purpose agent uses them via the Task tool system.

## Library Structure

### Agent Library (`agents/`)
Contains specialized agents organized by category (with a primary focus on MCPB/Claude Desktop extension development):
- **Orchestration**: Project coordination and approval workflows
- **Strategy & Planning**: Vision, requirements, and roadmap management  
- **Technical Implementation**: Contracts, code, tests, and environment setup
- **MCP Server Development**: Protocol schemas, server implementation, tools, resources, client integration, and testing
- **MCP Bundle Development**: Manifests, configuration, implementation, dependency bundling, packaging, and installation testing
- **Quality & Documentation**: Consistency auditing and documentation maintenance

### Available Agents in Library

**Quality & Auditing (Always Included):**
- `consistency-auditor`: Read-only drift detection across project files
- `bullshit-detector`: Reality-check audit for unrealistic claims with Context7 integration

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

**MCP Bundle Development (Claude Desktop extensions):**
- `mcpb-extension-manager`: Implement MCP server code for bundles (server/ directory only)
- `mcpb-manifest-builder`: Create and manage manifest.json files with user configuration
- `mcpb-packager`: Package bundles into .mcpb files using official MCPB CLI tools

**Quality & Documentation:**
- `documentation-maintainer`: Formatting, cross-references, consistency (no content creation)
- `improvements-manager`: Enhancement suggestions and optimization recommendations

## Using This Library

### Bootstrap New Projects
Use the `project-bootstrap` agent in this repo to create new customer projects (especially Claude Desktop MCPB extension projects):

```bash
Task: "Bootstrap new project for [description]"
Agent: project-bootstrap
```

The bootstrap agent will:
1. **Gather requirements** conversationally 
2. **Select appropriate agents** from the library based on project type
3. **Copy selected agents + rules** to new project location
4. **Generate project-specific README.md** with usage instructions
5. **Set up project structure** with `uv init` for Python projects (supports Python|Node.js|Binary runtimes)

### Library Maintenance
When working in this library repo:
- **Agents library**: Update agent definitions in `agents/`
- **Rules library**: Update behavioral rules in `agents-rules/`
- **Bootstrap tool**: Modify `.claude/agents/project-bootstrap.md`
- **Coordination framework**: Update `AGENTS.md` with dispatch patterns

### Example Bootstrap Usage
```bash
# Create MCP Bundle project
Task: "Bootstrap new MCP Bundle for Shopify order validation"
Agent: project-bootstrap

# Create MCP server project  
Task: "Bootstrap MCP server for file system operations"
Agent: project-bootstrap

# Create data pipeline project
Task: "Bootstrap Python project for CSV data processing"
Agent: project-bootstrap
```

### WTB Command Prefixes

**Recommended**: Use WTB-prefixed commands for namespace management and ownership signaling (notably for MCPB/Claude Desktop workflows):

```bash
# WTB command examples
/wtb:mcp-create-server weather-api --type python --template tools
/wtb:mcp-create-bundle weather-bundle --type python --package
/wtb:mcpb-manage-extension --all --strict
/wtb:validate-mcpb-bundle --schema --smoke-test --strict
```

## Context7 Integration

This library includes Context7 MCP server integration for enhanced agent capabilities:

### Setup (Recommended)
```bash
# Add Context7 MCP server globally
claude mcp add context7 --scope user -- npx -y @upstash/context7-mcp@latest

# Verify installation
claude mcp list
```

### Usage with Agents
Agents can access real-time documentation by including "use context7" in task prompts:

**Enhanced Agents:**
- **`bullshit-detector`**: Reality-check technical claims against current API documentation
- **`python-code-writer`**: Generate code using up-to-date examples and best practices  
- **`documentation-maintainer`**: Ensure examples match current API versions

**Example:**
```bash
Task: "Reality-check our MCP Bundle requirements against current Claude Desktop API capabilities. use context7 to verify manifest specifications and development limitations."
Agent: bullshit-detector
```

**Note**: Context7 works through natural language integration, not direct tool calls. Agents automatically receive real-time documentation context when "use context7" is included in prompts.

## Development Commands

This is a library repository. The main command is using the bootstrap agent to create new customer projects.

## Library File Structure

```
claude-code-subagents/                  # Agent library repository
├── .claude/
│   └── agents/
│       └── project-bootstrap.md       # Bootstrap tool for new projects
├── agents/                            # Agent library (21 agents)
│   ├── consistency-auditor.md
│   ├── [6 MCP agents...]
│   ├── [3 MCP Bundle agents...]
│   └── [remaining agents...]
├── agents-rules/                      # Rules library (21 rules)
│   ├── consistency-auditor-rules.md
│   └── [corresponding rules...]
├── AGENTS.md                          # Coordination framework for bootstrapped projects
├── README.md                          # Library documentation
└── CLAUDE.md                          # This file - library usage guide
```

## Notes

This is an **agent library repository**. The agents here get copied to customer projects where Claude Code's general-purpose agent uses them with the Task tool system.

For bootstrapped project usage patterns, see the `AGENTS.md` file which gets copied to each customer project.
