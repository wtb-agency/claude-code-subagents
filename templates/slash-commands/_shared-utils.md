# Shared Utility Functions for Slash Commands
# This file contains common patterns extracted from multiple commands
# Include this in commands using: !source .claude/slash-commands/_shared-utils.md

# Cross-platform compatibility
detect_os() {
    case "$(uname -s)" in
        CYGWIN*|MINGW*|MSYS*) echo "windows" ;;
        Darwin*) echo "macos" ;;
        Linux*) echo "linux" ;;
        *) echo "unknown" ;;
    esac
}

# Cross-platform shell safety
set_shell_safety() {
    set -euo pipefail 2>/dev/null || {
        # Fallback for shells that don't support pipefail
        set -eu
    }
}

# Cross-platform packaging command
get_pack_command() {
    local output_file="$1"
    local source_dir="$2"
    
    case "$(detect_os)" in
        windows)
            # Use Python zipfile on Windows (most reliable)
            echo "python -m zipfile -c \"$output_file\" \"$source_dir\""
            ;;
        *)
            # Use zip on Unix-like systems
            if command -v zip >/dev/null 2>&1; then
                echo "zip -r \"$output_file\" \"$source_dir\""
            else
                # Fallback to Python
                echo "python -m zipfile -c \"$output_file\" \"$source_dir\""
            fi
            ;;
    esac
}

# Project type detection (used in 3 commands)
detect_project_type() {
    local PROJECT_TYPE="general"
    
    if [ -f "manifest.json" ] && (grep -q '"type".*:.*"python"' manifest.json 2>/dev/null || grep -q '"type".*:.*"node"' manifest.json 2>/dev/null); then
        PROJECT_TYPE="dxt"
    elif [ -f "server.py" ] || [ -f "server/main.py" ] || [ -f "src/main.py" ]; then
        PROJECT_TYPE="mcp" 
    elif [ -f "pyproject.toml" ]; then
        PROJECT_TYPE="python"
    elif [ -f "package.json" ]; then
        PROJECT_TYPE="node"
    fi
    
    echo "$PROJECT_TYPE"
}

# Standard report header (used in 3 commands)
generate_report_header() {
    local title="$1"
    local emoji="$2"
    
    echo "$emoji $title"
    echo "$(printf '=%.0s' {1..${#title}})"
    echo ""
}

# Find implementation files (used in 3 commands)
find_implementation_files() {
    local file_types="$1"  # e.g., "py js ts"
    local max_files="${2:-50}"
    
    local find_args=""
    for ext in $file_types; do
        find_args="$find_args -name \"*.$ext\""
    done
    
    eval "find . $find_args -not -path './.*' -not -path './node_modules/*' -not -path './venv/*'" | head -n "$max_files"
}

# Find tool definition files  
find_tool_files() {
    find_implementation_files "py js ts" 50 | xargs grep -l "def.*tool\|function.*tool\|@tool\|tool.*=" 2>/dev/null || true
}

# Find resource definition files
find_resource_files() {
    find_implementation_files "py js ts" 50 | xargs grep -l "resource\|Resource\|@resource" 2>/dev/null || true
}

# JSON validation with error details
validate_json_file() {
    local file="$1"
    local schema_type="$2"
    
    if [ ! -f "$file" ]; then
        echo "❌ File not found: $file"
        return 1
    fi
    
    # Basic JSON syntax check
    if ! python3 -c "import json; json.load(open('$file'))" 2>/dev/null; then
        echo "❌ Invalid JSON syntax in: $file"
        return 1
    fi
    
    echo "✅ Valid JSON: $file"
    return 0
}

# Decision logging (used in 3 commands)
log_decision() {
    local action="$1"
    local context="$2"
    local approval_required="${3:-true}"
    
    if [ ! -f "docs/decisions.md" ]; then
        mkdir -p docs
        echo "# Decision Log" > docs/decisions.md
        echo "" >> docs/decisions.md
    fi
    
    local timestamp=$(date -u +"%Y-%m-%dT%H:%M:%SZ")
    local status="Pending"
    if [ "$approval_required" = "false" ]; then
        status="Auto-approved"
    fi
    
    echo "
## $timestamp - $action
**Status**: $status  
**Context**: $context
**Action**: $action
**Approval**: $([ "$approval_required" = "true" ] && echo "Pending human approval" || echo "Auto-approved")
" >> docs/decisions.md
}

# File size analysis
analyze_file_sizes() {
    local directory="$1"
    local file_pattern="$2"
    
    if [ -d "$directory" ]; then
        find "$directory" -name "$file_pattern" -exec du -h {} \; 2>/dev/null | sort -hr | head -10
    fi
}

# Common error messages
error_not_found() {
    local item="$1"
    echo "❌ $item not found. Please check your project structure."
}

error_invalid_format() {
    local file="$1"
    local expected="$2"
    echo "❌ Invalid format in $file. Expected: $expected"
}

# Success message with next steps
success_with_next_steps() {
    local action="$1"
    local next_command="$2"
    
    echo "✅ $action completed successfully!"
    echo ""
    if [ -n "$next_command" ]; then
        echo "💡 Next step: $next_command"
    fi
}

# Check if tools are installed
check_required_tools() {
    local tools="$1"  # space-separated list
    local missing=""
    
    for tool in $tools; do
        if ! command -v "$tool" >/dev/null 2>&1; then
            missing="$missing $tool"
        fi
    done
    
    if [ -n "$missing" ]; then
        echo "❌ Missing required tools:$missing"
        echo "Please install them and try again."
        return 1
    fi
    
    return 0
}

# Progress indicator for long operations
show_progress() {
    local message="$1"
    local step="$2"
    local total="$3"
    
    if [ -n "$step" ] && [ -n "$total" ]; then
        echo "[$step/$total] $message..."
    else
        echo "🔄 $message..."
    fi
}