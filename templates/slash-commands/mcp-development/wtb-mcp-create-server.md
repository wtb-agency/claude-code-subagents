---
description: "One-shot MCP server creator - scaffold, implement, validate, test"
argument-hint: "[server-name] [--type python|node|binary] [--template basic|tools|resources] [--test]"
allowed-tools: [Task, Read, Write, Bash, Glob]
---

One-shot MCP server creation orchestrator - scaffold, implement, validate, test in single pipeline.

**Usage**: `/wtb:mcp-create-server [server-name] [--type python|node|binary] [--template basic|tools|resources] [--test]`

**Core Parameters:**
- `server-name`: Name for the new MCP server
- `--type`: Runtime (python|node|binary) - defaults to python
- `--template`: Server template (basic|tools|resources) - defaults to basic
- `--test`: Run comprehensive testing after creation

**Templates:**
- `basic`: Simple MCP server with ping tool
- `tools`: MCP server focused on tool implementations  
- `resources`: MCP server focused on resource providers

**What this orchestrator does:**
1. **Scaffold**: Creates directory structure and basic server files
2. **Implement**: Generates server code using specialized agents
3. **Schema Check**: Validates MCP protocol schemas
4. **Smoke Test**: Tests MCP handshake and basic communication
5. **Optional Bundle**: Creates deployable package

---

*Full implementation available - use Task tool to delegate to mcp-server-engineer for complex server logic*