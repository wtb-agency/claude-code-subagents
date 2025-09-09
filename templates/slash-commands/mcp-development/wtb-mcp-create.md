---
description: "One-shot MCP Bundle creator - scaffold, implement, validate, package"
argument-hint: "[bundle-name] [--type python|node|binary] [--template basic|tools|resources] [--package]"
allowed-tools: [Task, Read, Write, Bash, Glob]
---

One-shot MCP Bundle creation orchestrator - scaffold, implement, validate, package in single pipeline.

**Usage**: `/wtb:mcp-create [bundle-name] [--type python|node|binary] [--template basic|tools|resources] [--package]`

**Core Parameters:**
- `bundle-name`: Name for the new MCP Bundle
- `--type`: Runtime (python|node|binary) - defaults to python
- `--template`: Bundle template (basic|tools|resources) - defaults to basic
- `--package`: Create .mcpb package after implementation

**Templates:**
- `basic`: Simple MCP server with ping tool
- `tools`: MCP server focused on tool implementations  
- `resources`: MCP server focused on resource providers

**What this orchestrator does:**
1. **Scaffold**: Creates bundle directory structure and manifest.json
2. **Implement**: Generates server code using specialized agents  
3. **Manifest**: Creates MCPB-compliant manifest with user configuration
4. **Validate**: Schema validation and smoke tests
5. **Package**: Creates installable .mcpb bundle file
6. **Install Hint**: Shows double-click installation instructions

---

*Full implementation available - use Task tool to delegate to mcpb-extension-manager and mcpb-manifest-builder for complex bundle logic*

```bash
#!/bin/bash
echo "This command is an orchestrator spec."
echo "Use Claude Code Task to dispatch:"
echo "  - Agent: mcpb-manifest-builder (create/update manifest)"
echo "  - Agent: mcpb-extension-manager (implement server in server/)"
echo "Then run: /wtb:mcpb-manage-extension --all"
```
