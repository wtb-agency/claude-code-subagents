---
description: "Complete MCPB pipeline - validate, bundle, package, test"
argument-hint: "[--validate] [--bundle] [--package] [--test] [--all] [--manifest-path] [--strict]"
allowed-tools: [Bash, Read, Write, Glob]
---

Complete MCPB pipeline orchestrator - validate, bundle, package, and test MCP Bundles.

**Usage**: `/wtb:mcpb-manage-extension [--validate] [--bundle] [--package] [--test] [--all] [--manifest-path] [--strict]`

**Parameters:**
- `--validate`: Run schema validation and smoke tests
- `--bundle`: Bundle server implementation
- `--package`: Create .mcpb package file
- `--test`: Run comprehensive testing
- `--all`: Execute complete pipeline (validate→bundle→package→test)
- `--manifest-path`: Path to manifest.json file
- `--strict`: Enable strict validation mode

**What this orchestrator does:**
1. **Validate**: Schema validation and MCPB compliance checks
2. **Bundle**: Server implementation bundling with dependencies
3. **Package**: Creates installable .mcpb file using cross-platform utilities
4. **Test**: Installation testing and runtime verification
5. **Report**: Comprehensive pipeline results and next steps

---

*Full implementation available - use Task tool to delegate to mcpb-extension-manager, mcpb-packager for specialized operations*

```bash
#!/bin/bash
echo "This command coordinates MCPB steps."
echo "Use Claude Code Task to dispatch:"
echo "  - Agent: mcpb-manifest-builder (manifest.json)"
echo "  - Agent: mcpb-extension-manager (server implementation)"
echo "Then package with: /wtb:mcpb-manage-extension --all"
```
