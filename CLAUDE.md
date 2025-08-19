# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Claude Code Subagent Framework** that implements strict role-based separation of concerns with human approval workflows. The framework ensures that different aspects of software development (orchestration, requirements, contracts, implementation, testing, documentation) are handled by specialized agents with clear boundaries and mandatory approval gates.

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

**Quality & Documentation:**
- `documentation-maintainer`: Formatting, cross-references, consistency (no content creation)
- `improvements-manager`: Enhancement suggestions and optimization recommendations

## Critical Constraints

### Role Boundaries
- **Only `project-orchestrator`** can edit `.claude/state.json`
- **Only `python-pro-software-engineer`** can edit `src/**`
- **Only `test-writer`** can edit `tests/**`
- **Only `data-contracts-manager`** can edit `contracts/**`
- **Only `dev-environment-maintainer`** can edit `pyproject.toml`

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
  development.md            # Dev environment setup

contracts/*.json            # Versioned JSON Schema contracts
src/                        # Production Python modules
tests/                      # Automated test suite
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
```

## Important Notes

- **Always use `uv` instead of `pip`** for Python dependency management
- **All changes require approval** - never bypass the workflow
- **Agents are stateless** - each dispatch is independent
- **Documentation is structural only** - content comes from specialized agents
- **Fail fast on approval gates** - stop immediately when pending decisions exist

This framework enforces disciplined development practices through role separation, approval workflows, and state management, ensuring quality and traceability in complex software projects.