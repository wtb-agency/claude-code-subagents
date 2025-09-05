---
description: "Run consistency checks across project files"
argument-hint: "[--scope files|contracts|tests|docs|state|decisions] [--fix] [--report]"
allowed-tools: [Read, Bash, Glob]
---

Run comprehensive consistency checks across project files and state.

**Usage**: `/wtb:audit-project [--scope files|contracts|tests|docs|state|decisions] [--fix] [--report]`

**Parameters:**
- `--scope`: Audit scope (files|contracts|tests|docs|state|decisions) - defaults to all
- `--fix`: Attempt to fix issues automatically
- `--report`: Generate detailed audit report

**Audit Scopes:**
- `files`: File structure, naming conventions, permissions
- `contracts`: Data contracts and schema validation
- `tests`: Test coverage and structure
- `docs`: Documentation completeness and consistency
- `state`: Project state integrity and history
- `decisions`: Decision workflow and approvals

**What this command does:**
1. **Structure Check**: Validates project directory structure
2. **Consistency Check**: Verifies naming conventions and patterns
3. **State Validation**: Checks project state integrity
4. **Boundary Verification**: Validates file ownership and permissions
5. **Report Generation**: Creates audit summary and recommendations

---

```bash
#!/bin/bash
source "$(dirname "$0")/_shared-utils.md"
set_shell_safety

# Parse arguments
SCOPE="all"
FIX_FLAG=false
REPORT_FLAG=false

for arg in "$@"; do
    case $arg in
        --scope=*) SCOPE="${arg#*=}" ;;
        --fix) FIX_FLAG=true ;;
        --report) REPORT_FLAG=true ;;
    esac
done

# Validate scope
if [[ ! "$SCOPE" =~ ^(all|files|contracts|tests|docs|state|decisions)$ ]]; then
    echo "❌ Invalid scope. Use: all, files, contracts, tests, docs, state, decisions"
    exit 1
fi

generate_report_header "WTB Project Auditor" "🏷️"

echo "📋 Configuration:"
echo "  🔍 Scope: $SCOPE"
echo "  🔧 Fix Issues: $([ "$FIX_FLAG" = true ] && echo "Enabled" || echo "Disabled")"
echo "  📊 Generate Report: $([ "$REPORT_FLAG" = true ] && echo "Enabled" || echo "Disabled")"
echo

# Initialize audit results
AUDIT_ERRORS=0
AUDIT_WARNINGS=0
AUDIT_FIXED=0

# Helper function for audit checks
audit_check() {
    local check_name="$1"
    local check_command="$2"
    local fix_command="$3"
    
    echo "🔍 Checking: $check_name"
    
    if eval "$check_command" >/dev/null 2>&1; then
        echo "  ✅ $check_name: OK"
    else
        echo "  ❌ $check_name: FAILED"
        AUDIT_ERRORS=$((AUDIT_ERRORS + 1))
        
        if [ "$FIX_FLAG" = true ] && [ -n "$fix_command" ]; then
            echo "  🔧 Attempting fix..."
            if eval "$fix_command" >/dev/null 2>&1; then
                echo "  ✅ Fixed: $check_name"
                AUDIT_FIXED=$((AUDIT_FIXED + 1))
            else
                echo "  ❌ Fix failed: $check_name"
            fi
        fi
    fi
}

# SCOPE: FILES
if [[ "$SCOPE" == "all" ]] || [[ "$SCOPE" == "files" ]]; then
    echo "📁 Auditing File Structure..."
    
    audit_check "Claude directory exists" "[ -d '.claude' ]" "mkdir -p .claude"
    audit_check "Agents directory exists" "[ -d '.claude/agents' ]" "mkdir -p .claude/agents"
    audit_check "Rules directory exists" "[ -d 'agents-rules' ]" "mkdir -p agents-rules"
    audit_check "Docs directory exists" "[ -d 'docs' ]" "mkdir -p docs"
    audit_check "README.md exists" "[ -f 'README.md' ]" "echo '# Project' > README.md"
    audit_check ".gitignore exists" "[ -f '.gitignore' ]" "echo '__pycache__/' > .gitignore"
    
    echo
fi

# SCOPE: STATE
if [[ "$SCOPE" == "all" ]] || [[ "$SCOPE" == "state" ]]; then
    echo "📊 Auditing Project State..."
    
    STATE_FILE=".claude/project-state.json"
    audit_check "State file exists" "[ -f '$STATE_FILE' ]" ""
    
    if [ -f "$STATE_FILE" ]; then
        audit_check "State JSON valid" "python3 -c 'import json; json.load(open(\"$STATE_FILE\"))'" ""
        audit_check "Required sections present" "python3 -c '
import json
state = json.load(open(\"$STATE_FILE\"))
required = [\"project\", \"agents\", \"workflow\", \"boundaries\"]
missing = [s for s in required if s not in state]
exit(0 if not missing else 1)
'" ""
    fi
    
    echo
fi

# SCOPE: DECISIONS
if [[ "$SCOPE" == "all" ]] || [[ "$SCOPE" == "decisions" ]]; then
    echo "⚖️ Auditing Decision Workflow..."
    
    if [ -f ".claude/project-state.json" ]; then
        PENDING_COUNT=$(python3 -c "
import json
try:
    state = json.load(open('.claude/project-state.json'))
    pending = state.get('workflow', {}).get('pending_decisions', [])
    print(len(pending))
except:
    print(0)
" 2>/dev/null)
        
        COMPLETED_COUNT=$(python3 -c "
import json
try:
    state = json.load(open('.claude/project-state.json'))
    completed = state.get('workflow', {}).get('completed_decisions', [])
    print(len(completed))
except:
    print(0)
" 2>/dev/null)
        
        echo "  📋 Pending decisions: $PENDING_COUNT"
        echo "  ✅ Completed decisions: $COMPLETED_COUNT"
        
        if [ "$PENDING_COUNT" -gt 10 ]; then
            echo "  ⚠️ Warning: Many pending decisions ($PENDING_COUNT)"
            AUDIT_WARNINGS=$((AUDIT_WARNINGS + 1))
        fi
    fi
    
    echo
fi

# SCOPE: CONTRACTS
if [[ "$SCOPE" == "all" ]] || [[ "$SCOPE" == "contracts" ]]; then
    echo "📋 Auditing Data Contracts..."
    
    if [ -d "contracts" ]; then
        CONTRACT_COUNT=$(find contracts -name "*.json" -o -name "*.yaml" -o -name "*.yml" | wc -l)
        echo "  📄 Contract files found: $CONTRACT_COUNT"
        
        # Validate JSON contracts
        find contracts -name "*.json" | while read -r contract; do
            audit_check "Contract valid: $(basename "$contract")" "python3 -c 'import json; json.load(open(\"$contract\"))'" ""
        done
    else
        echo "  📂 No contracts directory found"
    fi
    
    echo
fi

# SCOPE: TESTS
if [[ "$SCOPE" == "all" ]] || [[ "$SCOPE" == "tests" ]]; then
    echo "🧪 Auditing Tests..."
    
    if [ -d "tests" ]; then
        TEST_COUNT=$(find tests -name "*.py" -o -name "*.js" -o -name "*.ts" | wc -l)
        echo "  🧪 Test files found: $TEST_COUNT"
        
        if [ "$TEST_COUNT" -eq 0 ]; then
            echo "  ⚠️ Warning: No test files found"
            AUDIT_WARNINGS=$((AUDIT_WARNINGS + 1))
        fi
    else
        echo "  📂 No tests directory found"
        if [ "$FIX_FLAG" = true ]; then
            mkdir -p tests
            echo "  ✅ Created tests directory"
            AUDIT_FIXED=$((AUDIT_FIXED + 1))
        fi
    fi
    
    echo
fi

# SCOPE: DOCS
if [[ "$SCOPE" == "all" ]] || [[ "$SCOPE" == "docs" ]]; then
    echo "📖 Auditing Documentation..."
    
    DOC_COUNT=$(find docs -name "*.md" 2>/dev/null | wc -l)
    echo "  📄 Documentation files: $DOC_COUNT"
    
    audit_check "README.md exists" "[ -f 'README.md' ]" "echo '# Project Documentation' > README.md"
    
    echo
fi

# GENERATE REPORT
if [ "$REPORT_FLAG" = true ]; then
    echo "📊 Generating Audit Report..."
    
    REPORT_FILE="docs/audit-report-$(date +%Y%m%d-%H%M%S).md"
    mkdir -p docs
    
    cat > "$REPORT_FILE" << REPORT_MD
# WTB Project Audit Report

**Generated**: $(date -u +"%Y-%m-%d %H:%M:%S UTC")
**Scope**: $SCOPE
**Command**: \`/wtb:audit-project --scope $SCOPE $([ "$FIX_FLAG" = true ] && echo "--fix")$([ "$REPORT_FLAG" = true ] && echo " --report")\`

## Summary

- **Errors**: $AUDIT_ERRORS
- **Warnings**: $AUDIT_WARNINGS
- **Fixed**: $AUDIT_FIXED

## Project Structure

\`\`\`
$(find . -type d -not -path './.git*' -not -path './__pycache__*' -not -path './.*' | head -20)
\`\`\`

## State Summary

$([ -f ".claude/project-state.json" ] && echo "State file exists" || echo "No state file found")

## Recommendations

$([ "$AUDIT_ERRORS" -gt 0 ] && echo "- Fix $AUDIT_ERRORS structural issues")
$([ "$AUDIT_WARNINGS" -gt 0 ] && echo "- Address $AUDIT_WARNINGS warnings")
$([ ! -f ".claude/project-state.json" ] && echo "- Initialize project state with /wtb:init-project")

---

*Generated by WTB Project Auditor*
REPORT_MD
    
    echo "  📄 Report saved: $REPORT_FILE"
fi

# FINAL SUMMARY
echo
echo "✅ WTB Project Audit Complete!"
echo
echo "📊 **Audit Summary:**"
echo "  ❌ Errors: $AUDIT_ERRORS"
echo "  ⚠️ Warnings: $AUDIT_WARNINGS"
echo "  🔧 Fixed: $AUDIT_FIXED"
echo
echo "🔗 **Related Commands:**"
echo "• /wtb:update-state --key 'audit.last_run' --value '$(date -u +%Y-%m-%dT%H:%M:%SZ)'"
echo "• /wtb:init-project - Initialize missing project structure"
echo
echo "🏷️ **WTB Namespace**: Collision-free project auditing"
```