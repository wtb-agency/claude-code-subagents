# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Claude Code Subagent Framework** that implements strict role-based separation of concerns with human approval workflows. The framework ensures that different aspects of software development (orchestration, requirements, contracts, implementation, testing, documentation, MCP server development) are handled by specialized agents with clear boundaries and mandatory approval gates.

## Core Architecture

### State-Driven Workflow
- **Central State**: All project state is managed in `.claude/state.json` by the `project-orchestrator` only
- **Decision Tracking**: All changes require entries in `docs/decisions.md` with Pending status until human approval
- **Approval Gates**: Every agent stops and waits for explicit human approval before changes become authoritative

### Agent Hierarchy & Responsibilities

**Orchestration Layer:**
- `project-orchestrator`: Manages `.claude/state.json`, enforces approval gates, dispatches other agents
- `consistency-auditor`: Read-only audits across contracts, code, docs, and state (no changes)

**Strategy & Planning:**
- `product-vision-manager`: Vision, mission, values (strategy level)
- `product-requirements-manager`: Functional requirements translation (what to build)
- `high-level-project-manager`: Phases and milestones (when to build)
- `low-level-project-manager`: Task breakdowns (how to organize)

**Technical Implementation:**
- `data-contracts-manager`: JSON Schema contracts in `contracts/` with versioning
- `python-pro-software-engineer`: Production code in `src/` only (no tests, no docs)
- `test-writer`: Automated tests in `tests/` only
- `dev-environment-maintainer`: `pyproject.toml`, `uv` dependencies, dev setup

**MCP Server Development:**
- `mcp-protocol-manager`: MCP specification compliance and protocol schema contracts
- `mcp-server-engineer`: Core MCP server runtime implementation
- `mcp-tools-manager`: MCP tool schemas and execution handlers
- `mcp-resources-manager`: MCP resource schemas and provider implementations
- `mcp-client-integration-manager`: Client configurations and integration guides
- `mcp-test-engineer`: MCP-specific testing and protocol validation

**Quality & Documentation:**
- `documentation-maintainer`: Formatting, cross-references, consistency (no content creation)
- `improvements-manager`: Enhancement suggestions and optimization recommendations

## Critical Constraints

### Role Boundaries
- **Only `project-orchestrator`** can edit `.claude/state.json`
- **Only `python-pro-software-engineer`** can edit `src/**` (excluding `src/mcp/**`)
- **Only `test-writer`** can edit `tests/**` (excluding `tests/mcp/**`)
- **Only `data-contracts-manager`** can edit `contracts/**` (excluding `contracts/mcp/**`)
- **Only `dev-environment-maintainer`** can edit `pyproject.toml`
- **Only `mcp-server-engineer`** can edit `src/mcp/server/**`
- **Only `mcp-tools-manager`** can edit `src/mcp/tools/**` and `contracts/mcp/tools/**`
- **Only `mcp-resources-manager`** can edit `src/mcp/resources/**` and `contracts/mcp/resources/**`
- **Only `mcp-protocol-manager`** can edit `contracts/mcp/**` (general protocols)
- **Only `mcp-client-integration-manager`** can edit `configs/mcp-clients/**`
- **Only `mcp-test-engineer`** can edit `tests/mcp/**`

### Approval Workflow
1. Agent performs its specialized task
2. Agent logs Pending decision in `docs/decisions.md`
3. Agent **STOPS** and waits for human approval
4. Human provides explicit approval/rejection
5. State is updated with approval status and timestamp

### Never Assume Next Steps
When `project-orchestrator` completes, Claude Code must:
- Summarize what the orchestrator did
- Point to `docs/decisions.md` for review
- **NEVER** propose next actions or dispatch other agents
- Wait for explicit human instructions

## Development Commands

Since this is a framework/template repository, there are no traditional build/test commands. The workflow is entirely agent-driven through Claude Code's Task tool.

## Key File Structure

```
.claude/
  agents/                   # Subagent definitions (runtime)
  state.json                # Central project state (orchestrator only)

docs/
  decisions.md              # Decision log with approval workflow
  requirements.md           # Product requirements
  roadmap.md                # High-level project phases
  contracts.md              # Contract version index
  mcp-contracts.md          # MCP protocol version index
  mcp-integration/          # MCP client integration guides
  development.md            # Dev environment setup

contracts/
  *.json                    # Versioned JSON Schema contracts
  mcp/                      # MCP protocol and capability contracts
    tools/                  # MCP tool schemas
    resources/              # MCP resource schemas

configs/
  mcp-clients/              # Client-specific MCP configurations

src/                        # Production Python modules
  mcp/                      # MCP server implementation
    server/                 # Core server runtime
    tools/                  # Tool handlers
    resources/              # Resource providers

tests/                      # Automated test suite
  mcp/                      # MCP-specific tests

agents-rules/               # Agent-specific behavior rules (template)
```

## Usage Pattern

1. **Always use the Task tool** to dispatch specialized agents
2. **Never edit files directly** - use appropriate agents
3. **Respect approval gates** - stop after Pending decisions
4. **Follow orchestrator rules** - no next-step assumptions
5. **Maintain role separation** - each agent has strict scope

## Agent Dispatch Examples

```bash
# Project initialization
Task: "Initialize project state for order processing system"
Agent: project-orchestrator

# Requirements definition  
Task: "Define requirements for Excel order uploads"
Agent: product-requirements-manager

# Contract creation
Task: "Create JSON schema for normalized order objects"
Agent: data-contracts-manager

# Implementation
Task: "Implement Excel parser per approved contract v1"
Agent: python-pro-software-engineer

# Testing
Task: "Write tests for src/excel_parser/"
Agent: test-writer

# Environment setup
Task: "Add pandas and openpyxl dependencies"
Agent: dev-environment-maintainer

# Consistency check
Task: "Audit repository for contract/code drift"
Agent: consistency-auditor

# MCP Protocol Definition
Task: "Define MCP tools capability schema v1"
Agent: mcp-protocol-manager

# MCP Server Implementation  
Task: "Implement MCP server with stdio transport"
Agent: mcp-server-engineer

# MCP Tool Development
Task: "Create file system tools for MCP server"
Agent: mcp-tools-manager

# MCP Client Integration
Task: "Add Claude Desktop integration config"
Agent: mcp-client-integration-manager

# MCP Testing
Task: "Write MCP protocol compliance tests"
Agent: mcp-test-engineer
```

## Important Notes

- **Always use `uv` instead of `pip`** for Python dependency management
- **All changes require approval** - never bypass the workflow
- **Agents are stateless** - each dispatch is independent
- **Documentation is structural only** - content comes from specialized agents
- **Fail fast on approval gates** - stop immediately when pending decisions exist

This framework enforces disciplined development practices through role separation, approval workflows, and state management, ensuring quality and traceability in complex software projects.