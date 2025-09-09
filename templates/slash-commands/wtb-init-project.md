---
description: "Initialize project with state tracking and approval workflow"
argument-hint: "[project-name] [--type python|mcp|mcpb|general] [--state-file]"
allowed-tools: [Read, Write, Bash, LS]
---

Initialize new projects with state management, approval workflow, and boundary enforcement patterns.

**Usage**: `/wtb:init-project [project-name] [--type python|mcp|mcpb|general] [--state-file]`

**Parameters:**
- `project-name`: Name for the new project
- `--type`: Project type (python|mcp|mcpb|general) - defaults to general
- `--state-file`: Custom state file path - defaults to .claude/project-state.json

**Project Types:**
- `python`: Python project with pyproject.toml, uv dependencies
- `mcp`: MCP server development with protocol schemas  
- `mcpb`: MCP Bundle development with manifest.json
- `general`: Generic project with basic structure

**What this command does:**
1. **Project Structure**: Creates directory structure and basic files
2. **State Management**: Initializes project state tracking file
3. **Approval Workflow**: Sets up decision logging and approval patterns
4. **Boundary Enforcement**: Configures file ownership and access controls
5. **Documentation**: Generates project-specific README and usage guide

---

```bash
#!/bin/bash
# Robust utility sourcing
if [ -f .claude/slash-commands/_shared-utils.md ]; then
  # shellcheck disable=SC1091
  . .claude/slash-commands/_shared-utils.md
else
  SCRIPT_DIR=$(dirname "$0" 2>/dev/null || pwd)
  if [ -f "$SCRIPT_DIR/_shared-utils.md" ]; then
    # shellcheck disable=SC1091
    . "$SCRIPT_DIR/_shared-utils.md"
  else
    echo "❌ Unable to locate _shared-utils.md"
    exit 1
  fi
fi
set_shell_safety

# Parse arguments
PROJECT_NAME="${1:-my-project}"
TYPE_ARG=""
STATE_FILE_ARG=""

for arg in "$@"; do
    case $arg in
        --type=*) TYPE_ARG="${arg#*=}" ;;
        --state-file=*) STATE_FILE_ARG="${arg#*=}" ;;
    esac
done

# Set defaults
PROJECT_TYPE="${TYPE_ARG:-general}"
STATE_FILE="${STATE_FILE_ARG:-.claude/project-state.json}"

# Validate arguments
if [[ ! "$PROJECT_TYPE" =~ ^(python|mcp|mcpb|general)$ ]]; then
    echo "❌ Invalid project type. Use: python, mcp, mcpb, general"
    exit 1
fi

generate_report_header "WTB Project Initializer" "🏷️"

echo "📋 Configuration:"
echo "  📁 Project Name: $PROJECT_NAME"
echo "  🔧 Project Type: $PROJECT_TYPE"
echo "  📄 State File: $STATE_FILE"
echo

# STAGE 1: CREATE PROJECT DIRECTORY
echo "📁 Creating project directory: $PROJECT_NAME/"
mkdir -p "$PROJECT_NAME" || {
    echo "❌ Failed to create project directory"
    exit 1
}
cd "$PROJECT_NAME" || {
    echo "❌ Failed to enter project directory"
    exit 1
}

# STAGE 2: INITIALIZE PROJECT STRUCTURE
echo "🏗️ Setting up project structure..."
mkdir -p .claude/{agents,slash-commands,hooks}
mkdir -p agents-rules
mkdir -p docs

# STAGE 3: CREATE STATE MANAGEMENT
echo "📊 Initializing state management..."
cat > "$STATE_FILE" << 'STATE_JSON'
{
  "version": "1.0",
  "project": {
    "name": "PROJECT_NAME_PLACEHOLDER",
    "type": "PROJECT_TYPE_PLACEHOLDER", 
    "created": "TIMESTAMP_PLACEHOLDER",
    "initialized_by": "wtb:init-project"
  },
  "agents": {
    "active": [],
    "boundaries": {},
    "permissions": {}
  },
  "workflow": {
    "approval_required": true,
    "pending_decisions": [],
    "completed_decisions": []
  },
  "boundaries": {
    "file_ownership": {},
    "write_permissions": {},
    "read_permissions": {}
  }
}
STATE_JSON

# Replace placeholders
sed -i.bak "s/PROJECT_NAME_PLACEHOLDER/$PROJECT_NAME/g" "$STATE_FILE"
sed -i.bak "s/PROJECT_TYPE_PLACEHOLDER/$PROJECT_TYPE/g" "$STATE_FILE"
sed -i.bak "s/TIMESTAMP_PLACEHOLDER/$(date -u +%Y-%m-%dT%H:%M:%SZ)/g" "$STATE_FILE"
rm "${STATE_FILE}.bak"

# STAGE 4: PROJECT TYPE SPECIFIC SETUP
echo "⚙️ Configuring for project type: $PROJECT_TYPE..."

case $PROJECT_TYPE in
    python)
        echo "🐍 Setting up Python project..."
        if command -v uv >/dev/null 2>&1; then
            uv init --python 3.11 || echo "⚠️ uv init failed, continuing..."
        else
            echo "requirements.txt" > requirements.txt
            echo "# $PROJECT_NAME" > README.md
        fi
        echo "__pycache__/" > .gitignore
        echo ".venv/" >> .gitignore
        mkdir -p src tests
        ;;
    mcp)
        echo "🔌 Setting up MCP server project..."
        mkdir -p src schemas tests
        echo "# $PROJECT_NAME MCP Server" > README.md
        echo "MCP Server implementation" > src/server.py
        ;;
    mcpb)
        echo "📦 Setting up MCP Bundle project (Claude Desktop extension)..."
        mkdir -p server
        echo "# $PROJECT_NAME MCP Bundle" > README.md
        echo "Use '/wtb:mcpb-manage-extension --all' or ask Claude Code to run mcpb-manifest-builder to create manifest.json" >> README.md
        ;;
    general)
        echo "📄 Setting up general project..."
        echo "# $PROJECT_NAME" > README.md
        echo "General project initialized with WTB patterns" >> README.md
        ;;
esac

# STAGE 5: CREATE WORKFLOW HOOKS
echo "🪝 Setting up workflow hooks..."
mkdir -p .claude/hooks

# Boundary enforcement hook
cat > .claude/hooks/boundary-enforcement.json << 'HOOK_JSON'
{
  "hooks": [
    {
      "name": "Enforce Agent Boundaries",
      "description": "Prevent agents from editing files outside their ownership boundaries",
      "events": ["PreToolUse"],
      "matchTool": "Edit",
      "command": [
        "bash", "-c",
        "# Get file being edited from tool input\nFILE_PATH=$(echo \"$CLAUDE_HOOK_INPUT\" | jq -r '.file_path // empty')\n\nif [ -z \"$FILE_PATH\" ]; then\n  echo 'No file path found in Edit tool'\n  exit 0\nfi\n\n# Protect critical system files that should only be modified via slash commands\ncase \"$FILE_PATH\" in\n  \".claude/project-state.json\")\n    echo \"{\\\"permissionDecision\\\": \\\"ask\\\", \\\"message\\\": \\\"State file modification detected. Use /wtb:update-state command instead.\\\"}\"\n    exit 0\n    ;;\n  \".claude/slash-commands/\"*)\n    echo \"{\\\"permissionDecision\\\": \\\"ask\\\", \\\"message\\\": \\\"Slash command modification detected. Are you sure you want to modify orchestration commands?\\\"}\"\n    exit 0\n    ;;\n  \".claude/hooks/\"*)\n    echo \"{\\\"permissionDecision\\\": \\\"ask\\\", \\\"message\\\": \\\"Hook configuration modification detected. Are you sure you want to modify workflow automation?\\\"}\"\n    exit 0\n    ;;\n  \"agents-rules/\"*)\n    echo \"{\\\"permissionDecision\\\": \\\"ask\\\", \\\"message\\\": \\\"Agent rules modification detected. This affects agent behavior boundaries.\\\"}\"\n    exit 0\n    ;;\n  \"AGENTS.md\")\n    echo \"{\\\"permissionDecision\\\": \\\"ask\\\", \\\"message\\\": \\\"Coordination framework modification detected. This affects agent orchestration rules.\\\"}\"\n    exit 0\n    ;;\nesac\n\n# Allow most operations - boundary enforcement primarily protects system files\necho '{\"permissionDecision\": \"allow\"}'"
      ]
    }
  ]
}
HOOK_JSON

# Approval workflow hook
cat > .claude/hooks/approval-workflow.json << 'HOOK_JSON'
{
  "hooks": [
    {
      "name": "Log Planning Agent Decisions",
      "description": "Automatically log decisions from planning agents and require approval",
      "events": ["PostToolUse"],
      "matchTool": ["Edit", "Write", "MultiEdit"],
      "command": [
        "bash", "-c",
"# Get tool details from input\nTOOL_NAME=$(echo \"$CLAUDE_HOOK_INPUT\" | jq -r '.tool // empty')\nFILE_PATH=$(echo \"$CLAUDE_HOOK_INPUT\" | jq -r '.file_path // empty')\n\nif [ -z \"$FILE_PATH\" ]; then\n  exit 0\nfi\n\n# Skip logging for system files that don't need approval\ncase \"$FILE_PATH\" in\n  \".claude/\"*|\"agents-rules/\"*|\"AGENTS.md\")\n    exit 0\n    ;;\nesac\n\n# Log decisions for key project files that agents modify\nTIMESTAMP=$(date -u +\"%Y-%m-%dT%H:%M:%SZ\")\n\ncase \"$FILE_PATH\" in\n  \"docs/requirements.md\")\n    cat >> docs/decisions.md << EOF\n\n## $TIMESTAMP - Requirements Update\n**Status**: Pending\n**Context**: Requirements modified - please review changes\n**Action**: Updated functional requirements in docs/requirements.md\n**Approval**: Awaiting human approval\n\nEOF\n    echo \"📝 Requirements change logged as pending decision\"\n    ;;\n  \"docs/vision.md\")\n    cat >> docs/decisions.md << EOF\n\n## $TIMESTAMP - Vision/Mission Update\n**Status**: Pending\n**Context**: Vision/mission modified - please review changes\n**Action**: Updated strategy artifacts in docs/vision.md\n**Approval**: Awaiting human approval\n\nEOF\n    echo \"📝 Vision change logged as pending decision\"\n    ;;\n  \"docs/roadmap.md\")\n    cat >> docs/decisions.md << EOF\n\n## $TIMESTAMP - Roadmap Update\n**Status**: Pending\n**Context**: Project roadmap modified - please review changes\n**Action**: Updated phases and milestones in docs/roadmap.md\n**Approval**: Awaiting human approval\n\nEOF\n    echo \"📝 Roadmap change logged as pending decision\"\n    ;;\n  \"docs/backlog.md\")\n    cat >> docs/decisions.md << EOF\n\n## $TIMESTAMP - Backlog Update\n**Status**: Pending\n**Context**: Work breakdown modified - please review changes\n**Action**: Updated task breakdown in docs/backlog.md\n**Approval**: Awaiting human approval\n\nEOF\n    echo \"📝 Backlog change logged as pending decision\"\n    ;;\n  contracts/*)\n    CONTRACT_NAME=$(basename \"$FILE_PATH\")\n    cat >> docs/decisions.md << EOF\n\n## $TIMESTAMP - Contract Update\n**Status**: Pending\n**Context**: Data contract modified - please review changes\n**Action**: Updated contract: $CONTRACT_NAME\n**Approval**: Awaiting human approval\n\nEOF\n    echo \"📝 Contract change logged as pending decision\"\n    ;;\n  src/*|tests/*)\n    COMPONENT=$(dirname \"$FILE_PATH\")\n    cat >> docs/decisions.md << EOF\n\n## $TIMESTAMP - Code Update\n**Status**: Pending\n**Context**: Code modified in $COMPONENT - please review implementation\n**Action**: Updated $(basename \"$FILE_PATH\")\n**Approval**: Awaiting human approval\n\nEOF\n    echo \"📝 Code change logged as pending decision\"\n    ;;\nesac"
      ]
    },
    {
      "name": "Notify on Approval Needed",
      "description": "Send notification when decisions are pending approval",
      "events": ["PostToolUse"],
      "matchTool": ["Edit", "Write", "MultiEdit"],
      "command": [
        "bash", "-c",
        "# Check for pending decisions\nif [ -f \"docs/decisions.md\" ]; then\n  PENDING_COUNT=$(grep -c \"Status.*: Pending\" docs/decisions.md 2>/dev/null || echo 0)\n  \n  if [ $PENDING_COUNT -gt 0 ]; then\n    echo \"\"\n    echo \"⏳ $PENDING_COUNT pending decision(s) require approval\"\n    echo \"   Use /wtb:approve-decision --latest to approve the newest\"\n    echo \"   Or run /wtb:approve-decision --list to see all pending decisions\"\n  fi\nfi"
      ]
    }
  ]
}
HOOK_JSON

# STAGE 6: GENERATE PROJECT README
echo "📖 Generating project documentation..."
cat > README.md << 'README_MD'
# PROJECT_NAME_PLACEHOLDER

PROJECT_TYPE_PLACEHOLDER project initialized with WTB patterns.

## Quick Start

This project uses the WTB (WTB) namespace for all commands:

```bash
# Project management
/wtb:update-state --key "project.status" --value "development"
/wtb:approve-decision --latest
/wtb:audit-project --scope files --report

# Development (type-specific commands available)
# See .claude/slash-commands/ for available commands
```

## Project Structure

- `.claude/`: Claude Code configuration and state management
- `agents-rules/`: Agent behavioral rules and boundaries
- `docs/`: Project documentation
- Project state: `.claude/project-state.json`

## State Management

This project uses centralized state management via `/wtb:update-state`:

```bash
# Check current state
cat .claude/project-state.json

# Update state values
/wtb:update-state --key "phase" --value "implementation"
```

---

*Initialized with `/wtb:init-project` - WTB namespace for collision-free development*
README_MD

sed -i.bak "s/PROJECT_NAME_PLACEHOLDER/$PROJECT_NAME/g" README.md
sed -i.bak "s/PROJECT_TYPE_PLACEHOLDER/$PROJECT_TYPE/g" README.md
rm README.md.bak

# FINAL SUMMARY
echo
echo "✅ WTB Project Initialized Successfully!"
echo
echo "📁 **Project Details:**"
echo "  🏷️ Name: $PROJECT_NAME"
echo "  🔧 Type: $PROJECT_TYPE"
echo "  📊 State: $STATE_FILE"
echo
echo "🚀 **Next Steps:**"
echo "  # Check project state:"
echo "  cat $STATE_FILE"
echo
echo "  # Update project state:"
echo "  /wtb:update-state --key 'status' --value 'active'"
echo
echo "  # Audit project:"
echo "  /wtb:audit-project --scope files"
echo
echo "🔗 **Available Commands:**"
echo "• /wtb:update-state - Modify project state safely"
echo "• /wtb:approve-decision - Process approval workflow"
echo "• /wtb:audit-project - Run consistency checks"
echo

echo "🏷️ **WTB Namespace**: All commands use /wtb: prefix for collision-free operation"
```
