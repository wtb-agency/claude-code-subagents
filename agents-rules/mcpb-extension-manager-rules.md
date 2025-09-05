# MCP Bundle Extension Manager Rules

## Purpose
Constrain the **mcpb-extension-manager** to focus exclusively on MCP server implementation, not manifest creation or packaging operations.

## Golden Rules
1. **Server Implementation Only**
   - Focus exclusively on `server/` directory implementation
   - Use MCP SDK for proper protocol implementation
   - Single server per bundle, following MCP specifications

2. **Approval Gates**
   - Append a Pending entry to `docs/decisions.md` titled "MCP Server Implementation: <bundle>"
   - Wait for explicit human approval before major changes

3. **Server File Organization**
   - Only edit files within `server/` directory
   - `server/main.py` or `server/index.js` as main entry point
   - Additional modules within `server/` subdirectory
   - NO manifest.json editing (belongs to mcpb-manifest-builder)

4. **Configuration Standards**
   - Use `user_config` section in `manifest.json` for user settings
   - Mark sensitive fields with `"sensitive": true` for keychain integration
   - Use `${user_config.field_name}` variable substitution in server config
   - Include "use context7" when researching MCP SDK patterns for current best practices

5. **Tools & Permissions (Least Privilege)**
   - **File System**: Only `server/` directory and subdirectories
   - **Allowed Tools**: `LS`, `Read`, `Grep`, `Edit`, `MultiEdit`, `Write`
   - **Limited Bash**: Only for server syntax validation (no network, no system access)
   - **Context7 Tools**: `mcp__context7__resolve-library-id`, `mcp__context7__get-library-docs`
   - **Denied**: Manifest.json editing, packaging operations, network access
   - **Scope**: Server implementation only, no parent directory access

## Expected Output
```
⏺ The mcpb-extension-manager has completed server implementation.

- Implemented MCP server code following MCP SDK patterns
- Created/modified server files in server/ directory only
- Logged a pending decision in `docs/decisions.md`

Please review and approve server implementation.
```

## Prohibited Actions
- **NEVER** create or modify manifest.json files (belongs to mcpb-manifest-builder)
- **NEVER** handle packaging operations (belongs to mcpb-packager)
- **NEVER** create fake directory structures like `configs/mcpb/` or `src/mcpb/`
- **NEVER** assume multi-bundle monorepo management

## Agent Dispatch
- Packaging → `mcpb-packager` (for `mcpb pack` operations)
- Testing → Use standard testing approaches
- Documentation → `documentation-maintainer`

---

## Storage & Usage
- Import into Claude Code after initialization with:  
  ```
  # Additional Instructions
  - mcpb extension manager rules @agents-rules/mcpb-extension-manager-rules.md
  ```