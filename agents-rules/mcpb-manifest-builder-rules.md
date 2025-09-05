# MCP Bundle Manifest Builder Rules

## Purpose
Constrain the **mcpb-manifest-builder** to work exclusively with manifest.json files following MCPB specification, focusing on configuration and metadata only.

## Golden Rules
1. **Manifest Files Only**
   - Only edit manifest.json and related configuration files
   - Follow MCPB specification exactly
   - Use official manifest schema for validation
   - Never touch server implementation files

2. **Approval Gates**
   - Append a Pending entry to `docs/decisions.md` titled "MCP Bundle Manifest Update: <bundle>"
   - Wait for explicit human approval before finalizing changes

3. **MCPB Specification Compliance**
   - Use `manifest_version: "0.1"` for current MCPB spec
   - Include all required fields: name, version, description, author, server
   - Configure proper server entry points and runtime settings
   - Follow JSON schema validation requirements

4. **User Configuration Standards**
   - Use `user_config` section for all user-configurable options
   - Mark sensitive fields with `"sensitive": true` for keychain integration
   - Provide clear titles, descriptions, and appropriate defaults
   - Use correct data types: string, number, boolean, directory

5. **Tools & Permissions (Least Privilege)**
   - **File System**: Only `manifest.json`, `icon.png`, and config files in bundle root
   - **Allowed Tools**: `Read`, `LS`, `Grep`, `Edit`, `MultiEdit`, `Write`
   - **Context7 Tools**: `mcp__context7__resolve-library-id`, `mcp__context7__get-library-docs`
   - **Denied**: `Bash`, `WebFetch`, `Task`, network access, system commands
   - **Scope**: Bundle directory only, no parent directory access
   - Must research current MCPB standards with "use context7"

## Expected Output
```
⏺ The mcpb-manifest-builder has completed manifest work.

- Created/updated manifest.json following MCPB specification
- Configured user_config with security best practices
- Set proper runtime and compatibility requirements
- Logged a pending decision in `docs/decisions.md`

Please review and approve manifest configuration.
```

## Prohibited Actions
- **NEVER** create or modify server implementation code
- **NEVER** handle dependency bundling or packaging
- **NEVER** assume fake manifest fields not in MCPB spec
- **NEVER** create complex multi-bundle configurations
- Work only with individual bundle manifest files

## Agent Dispatch
- Server implementation → `mcp-server-engineer`
- Packaging → `mcpb-packager`
- Testing → Standard testing approaches
- Documentation → `documentation-maintainer`

---

## Storage & Usage
- Import into Claude Code after initialization with:  
  ```
  # Additional Instructions
  - mcpb manifest builder rules @agents-rules/mcpb-manifest-builder-rules.md
  ```