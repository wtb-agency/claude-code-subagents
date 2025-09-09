---
description: "Comprehensive MCPB bundle validation - schema + smoke tests"
argument-hint: "[--schema] [--smoke-test] [--strict] [--manifest-path]"
allowed-tools: [Read, Write, Bash, Glob, mcp__context7__resolve-library-id, mcp__context7__get-library-docs]
---

Comprehensive MCPB bundle validation with schema checks and smoke tests.

**Usage**: `/wtb:validate-mcpb-bundle [--schema] [--smoke-test] [--strict] [--manifest-path]`

**Parameters:**
- `--schema`: Run JSON schema validation against MCPB specification
- `--smoke-test`: Execute MCP handshake and basic connectivity tests
- `--strict`: Enable strict validation mode (fail on warnings)
- `--manifest-path`: Path to manifest.json file to validate

**Validation Checks:**
- Manifest JSON schema compliance
- Required fields validation (name, version, server config)
- MCP protocol handshake testing
- Entry point and dependency verification
- Security validation (no hardcoded secrets)
- Cross-platform compatibility checks

**What this validator does:**
1. **Schema Validation**: Validates manifest.json against official MCPB schema
2. **Structure Check**: Verifies required files and directory structure
3. **Smoke Tests**: Tests MCP server startup and basic functionality
4. **Security Scan**: Checks for hardcoded secrets and security issues
5. **Compliance Report**: Generates detailed validation report

---

*Full implementation available - use Context7 integration for latest MCPB specification validation*

```bash
#!/bin/bash
echo "This validator focuses on manifest + smoke tests."
echo "For latest spec checks, include 'use context7' when dispatching agents."
```
