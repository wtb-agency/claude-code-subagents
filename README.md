# Claude Code Subagent Library

Primary scope: This library is designed to develop Claude Desktop extensions built as MCP Bundles (MCPB). It also supports adjacent workflows like MCP server development and general Python projects.

A library of 21 specialized agents for Claude Code's Task tool, plus a bootstrap system to create focused customer projects. Features **8 optimized WTB slash commands** for MCP/MCPB development workflows alongside native Claude Code orchestration.

> **🏷️ WTB Namespace**: All commands use `/wtb:` prefixes for namespace management and ownership signaling.

## Overview

This library provides **21 specialized agents** and a **project bootstrap system** for building Claude Desktop extensions via MCP Bundles (primary), with additional support for MCP servers and general code projects:

- **Native Orchestration**: 8 optimized WTB slash commands and hooks for MCP/MCPB workflows
- **Strategy & Planning**: Vision, requirements, and roadmap management  
- **Technical Implementation**: Contracts, code, tests, and environment setup
- **MCP Server Development**: Protocol compliance, server implementation, tools, resources, client integration, and testing
- **MCP Bundle Development (Claude Desktop extensions)**: MCP Bundle manifests, configuration, implementation, dependency bundling, packaging, and installation testing
- **Quality & Documentation**: Consistency auditing and documentation maintenance

Agents get copied to customer projects where they work within file ownership boundaries and approval workflows.

### Terminology
- "Claude Desktop extension" → implemented as an MCP Bundle packaged as `.mcpb`

## How the Bootstrap System Works

### Project Bootstrap Process
1. **Conversation**: `project-bootstrap` agent gathers project requirements
2. **Analysis**: Determines which agents are needed based on project type
3. **Selection**: Copies only relevant agents from the library (not all 21)
4. **Setup**: Creates project structure, runs `uv init` for Python projects (supports Python|Node.js|Binary runtimes)
5. **Documentation**: Generates project-specific README.md with usage instructions

### Agent Selection Examples
- **Claude Desktop Extension (MCP Bundle)**: Copies MCP Bundle agents + 1 MCPB pipeline command + hooks (3 agents)
- **MCP Server**: Copies MCP agents + 4 MCP commands + hooks (6 agents) 
- **Data Pipeline**: Copies code/test agents + 4 orchestration commands + hooks (4 agents)
- **Full Project**: Copies planning + implementation agents + all 8 optimized commands + hooks (10+ agents)

### Bootstrapped Project Structure
Each customer project gets:
- Selected agents copied to `.claude/agents/`
- Corresponding rules copied to `agents-rules/`
- **8 optimized WTB slash commands** copied to `.claude/slash-commands/` (with special focus on MCPB workflows for Claude Desktop)
- Boundary enforcement hooks configuration
- `AGENTS.md` coordination framework
- Project-specific README.md with usage examples

## Agent Architecture

### Quality Assurance
- **`consistency-auditor`**: Read-only drift detection across contracts, code, and docs
- **`bullshit-detector`**: Reality-check audit for unrealistic claims, impossible timelines, and over-ambitious scope using real-time documentation via Context7 MCP

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

### MCP Bundle Development (Claude Desktop extensions)
- **`mcpb-extension-manager`**: Implement MCP server code for bundles (server/ directory only)
- **`mcpb-manifest-builder`**: Create and manage manifest.json files with user configuration  
- **`mcpb-packager`**: Package bundles into .mcpb files using official MCPB CLI tools

### Quality & Documentation
- **`documentation-maintainer`**: Formatting, cross-references, and structural consistency
- **`improvements-manager`**: Enhancement detection and optimization proposals

## Context7 MCP Server Setup (Recommended)

For enhanced agent capabilities with real-time documentation access:

```bash
# Add Context7 MCP server globally (user scope)
claude mcp add context7 --scope user -- npx -y @upstash/context7-mcp@latest

# Verify installation
claude mcp list
```

**Benefits**: Agents can include "use context7" in their queries to access current API documentation and examples automatically, eliminating outdated code generation. Context7 works through natural language integration, not direct tool calls.

## Bootstrap New Projects

Use the bootstrap agent to create focused customer projects:

**Bootstrap Usage:**
```bash
# Create MCP Bundle project
Task: "Bootstrap new MCP Bundle for Shopify order validation" 
Agent: project-bootstrap

# Create MCP server project
Task: "Bootstrap MCP server for file system operations"
Agent: project-bootstrap

# Create data pipeline project  
Task: "Bootstrap Python project for CSV processing"
Agent: project-bootstrap
```

**The bootstrap agent will gather requirements, select appropriate agents, and create a focused project setup with relevant slash commands.**

### WTB Command Examples

After bootstrapping, use **WTB commands** for collision-free development:

```bash
# One-shot MCP server creation
/wtb:mcp-create-server weather-api --type python --template tools

# One-shot MCP Bundle creation
/wtb:mcp-create weather-bundle --type python --package

# Complete MCPB development pipeline
/wtb:mcpb-manage-extension --all --strict

# Comprehensive bundle validation
/wtb:validate-mcpb-bundle --schema --smoke-test --strict

# Project management
/wtb:init-project my-mcp-project --type mcp
/wtb:update-state --key "status" --value "development"
/wtb:approve-decision dec-123 --action approve
/wtb:audit-project --scope all --report
```

## WTB-Only Command Library (8 Commands)

Focused WTB command interface using `/wtb:` namespace for collision-free operation:

### Core Project Management (4 commands)
- **`/wtb:init-project`**: Initialize project with state tracking and approval workflow
- **`/wtb:update-state`**: Safely modify project state with validation
- **`/wtb:approve-decision`**: Process pending decisions from approval workflow
- **`/wtb:audit-project`**: Run consistency checks across project files

### MCP Development (4 commands)
- **`/wtb:mcp-create-server`**: One-shot MCP server creator - scaffold, implement, validate, test
- **`/wtb:mcp-create`**: One-shot MCP Bundle creator - scaffold, implement, validate, package
- **`/wtb:mcpb-manage-extension`**: Complete MCPB pipeline - validate, bundle, package, test
- **`/wtb:validate-mcpb-bundle`**: Comprehensive MCPB validation - schema + smoke tests

These 8 focused commands provide a powerful toolkit for MCP server and MCP Bundle development with clean namespace management and consistent WTB branding.

## Library File Structure

```
claude-code-subagents/                  # This repository
├── .claude/
│   └── agents/
│       └── project-bootstrap.md       # Bootstrap tool
├── agents/                            # Agent library (21 agents)
│   ├── consistency-auditor.md  
│   ├── [6 MCP agents...]
│   ├── [3 MCP Bundle agents...]
│   └── [remaining agents...]
├── agents-rules/                      # Rules library (21 rules)
│   ├── consistency-auditor-rules.md
│   └── [corresponding rules...]
├── templates/                         # Templates for customer projects
│   ├── slash-commands/                # 8 WTB-only commands
│   │   ├── _shared-utils.md           # Cross-platform utility functions
│   │   ├── wtb-init-project.md        # Project initialization
│   │   ├── wtb-update-state.md        # State management
│   │   ├── wtb-approve-decision.md    # Workflow approvals
│   │   ├── wtb-audit-project.md       # Project auditing
│   │   ├── mcp-development/           # MCP server commands
│   │   │   ├── wtb-mcp-create-server.md     # One-shot server creator
│   │   │   └── wtb-mcp-create.md            # One-shot bundle creator
│   │   ├── mcpb-development/          # MCPB pipeline command
│   │   │   └── wtb-mcpb-manage-extension.md # Complete MCPB pipeline
│   │   └── quality-compliance/        # Validation commands
│   │       └── wtb-validate-mcpb-bundle.md  # Bundle validation
│   └── hooks/                         # Workflow automation
│       ├── boundary-enforcement.json
│       └── approval-workflow.json
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
