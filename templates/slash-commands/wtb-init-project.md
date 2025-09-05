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
source "$(dirname "$0")/_shared-utils.md"
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
        echo "📦 Setting up MCP Bundle project..."
        mkdir -p server
        cat > manifest.json << 'MANIFEST_JSON'
{
  "name": "PROJECT_NAME_PLACEHOLDER",
  "version": "1.0.0",
  "description": "MCP Bundle implementation",
  "server": {
    "type": "python",
    "entry_point": "server/main.py"
  },
  "mcp_config": {
    "command": "python",
    "args": ["server/main.py"]
  }
}
MANIFEST_JSON
        sed -i.bak "s/PROJECT_NAME_PLACEHOLDER/$PROJECT_NAME/g" manifest.json
        rm manifest.json.bak
        echo "# $PROJECT_NAME MCP Bundle" > README.md
        ;;
    general)
        echo "📄 Setting up general project..."
        echo "# $PROJECT_NAME" > README.md
        echo "General project initialized with WTB patterns" > README.md
        ;;
esac

# STAGE 5: CREATE BASIC HOOKS
echo "🪝 Setting up workflow hooks..."
cat > .claude/hooks/boundary-enforcement.json << 'HOOK_JSON'
{
  "pre-edit": {
    "description": "Check file ownership before edits",
    "command": "echo 'Checking boundaries...'",
    "enabled": true
  },
  "post-edit": {
    "description": "Update state after changes", 
    "command": "echo 'Updating project state...'",
    "enabled": true
  }
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
/wtb:update-state --key "status" --value "development"
/wtb:approve-decision <decision-id> --action approve
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