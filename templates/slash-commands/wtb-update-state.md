---
description: "Safely modify project state with validation"
argument-hint: "[--state-file] [--key] [--value] [--validate]"
allowed-tools: [Read, Write, Bash, LS]
---

Safely modify project state values with validation and audit logging.

**Usage**: `/wtb:update-state [--state-file] [--key] [--value] [--validate]`

**Parameters:**
- `--state-file`: Path to state file - defaults to .claude/project-state.json
- `--key`: State key to update (dot notation supported: project.status)
- `--value`: New value to set
- `--validate`: Run validation checks before updating

**Common State Keys:**
- `project.status`: Project status (planning|development|testing|production)
- `project.phase`: Development phase
- `workflow.approval_required`: Enable/disable approval workflow
- `agents.active`: List of active agents
- `boundaries.write_permissions`: File write permissions

**What this command does:**
1. **Validation**: Validates state file structure and key paths
2. **Backup**: Creates backup before changes
3. **Update**: Safely modifies state values
4. **Logging**: Records state changes with timestamps
5. **Verification**: Confirms changes were applied correctly

---

```bash
#!/bin/bash
source "$(dirname "$0")/_shared-utils.md"
set_shell_safety

# Parse arguments
STATE_FILE=".claude/project-state.json"
KEY=""
VALUE=""
VALIDATE_FLAG=false

for arg in "$@"; do
    case $arg in
        --state-file=*) STATE_FILE="${arg#*=}" ;;
        --key=*) KEY="${arg#*=}" ;;
        --value=*) VALUE="${arg#*=}" ;;
        --validate) VALIDATE_FLAG=true ;;
    esac
done

# Validate required arguments
if [[ -z "$KEY" ]] || [[ -z "$VALUE" ]]; then
    echo "❌ Missing required arguments: --key and --value"
    echo "Usage: /wtb:update-state --key 'project.status' --value 'development'"
    exit 1
fi

generate_report_header "WTB State Manager" "🏷️"

echo "📋 Configuration:"
echo "  📄 State File: $STATE_FILE"
echo "  🔑 Key: $KEY"
echo "  💎 Value: $VALUE"
echo "  ✅ Validate: $([ "$VALIDATE_FLAG" = true ] && echo "Enabled" || echo "Disabled")"
echo

# Check if state file exists
if [[ ! -f "$STATE_FILE" ]]; then
    echo "❌ State file not found: $STATE_FILE"
    echo "💡 Initialize project first: /wtb:init-project"
    exit 1
fi

# STAGE 1: VALIDATION
if [ "$VALIDATE_FLAG" = true ]; then
    echo "🔍 Validating state file structure..."
    
    # Basic JSON validation
    if ! python3 -c "import json; json.load(open('$STATE_FILE'))" 2>/dev/null; then
        echo "❌ Invalid JSON in state file"
        exit 1
    fi
    echo "  ✅ JSON structure valid"
    
    # Check required sections exist
    for section in "project" "agents" "workflow" "boundaries"; do
        if ! python3 -c "import json; data=json.load(open('$STATE_FILE')); data['$section']" 2>/dev/null; then
            echo "❌ Missing required section: $section"
            exit 1
        fi
    done
    echo "  ✅ Required sections present"
fi

# STAGE 2: BACKUP
echo "💾 Creating backup..."
BACKUP_FILE="${STATE_FILE}.backup.$(date +%s)"
cp "$STATE_FILE" "$BACKUP_FILE"
echo "  📁 Backup created: $BACKUP_FILE"

# STAGE 3: UPDATE STATE
echo "🔄 Updating state..."

# Use Python for safe JSON manipulation
python3 << PYTHON_SCRIPT
import json
import sys
from datetime import datetime

state_file = "$STATE_FILE"
key_path = "$KEY"
new_value = "$VALUE"

try:
    # Load current state
    with open(state_file, 'r') as f:
        state = json.load(f)
    
    # Navigate to the key using dot notation
    keys = key_path.split('.')
    target = state
    
    # Navigate to parent of target key
    for key in keys[:-1]:
        if key not in target:
            target[key] = {}
        target = target[key]
    
    # Store old value for logging
    final_key = keys[-1]
    old_value = target.get(final_key, None)
    
    # Update the value
    target[final_key] = new_value
    
    # Add to change log
    if 'change_log' not in state:
        state['change_log'] = []
    
    state['change_log'].append({
        'timestamp': datetime.utcnow().isoformat() + 'Z',
        'key': key_path,
        'old_value': old_value,
        'new_value': new_value,
        'changed_by': 'wtb:update-state'
    })
    
    # Write updated state
    with open(state_file, 'w') as f:
        json.dump(state, f, indent=2)
    
    print(f"  ✅ Updated {key_path}: {old_value} → {new_value}")
    
except Exception as e:
    print(f"❌ Failed to update state: {e}")
    sys.exit(1)
PYTHON_SCRIPT

if [ $? -ne 0 ]; then
    echo "🔙 Restoring from backup..."
    cp "$BACKUP_FILE" "$STATE_FILE"
    exit 1
fi

# STAGE 4: VERIFICATION
echo "🔍 Verifying changes..."
CURRENT_VALUE=$(python3 -c "
import json
with open('$STATE_FILE', 'r') as f:
    state = json.load(f)
keys = '$KEY'.split('.')
target = state
for key in keys:
    target = target[key]
print(target)
" 2>/dev/null)

if [[ "$CURRENT_VALUE" == "$VALUE" ]]; then
    echo "  ✅ Verification successful"
else
    echo "  ⚠️ Verification failed - value mismatch"
fi

# STAGE 5: CLEANUP
echo "🧹 Cleanup..."
# Keep only last 5 backups
ls -t "${STATE_FILE}.backup."* 2>/dev/null | tail -n +6 | xargs rm -f 2>/dev/null || true
echo "  ✅ Old backups cleaned"

# FINAL SUMMARY
echo
echo "✅ WTB State Update Complete!"
echo
echo "📊 **Change Summary:**"
echo "  🔑 Key: $KEY"
echo "  💎 New Value: $VALUE"
echo "  📄 State File: $STATE_FILE"
echo
echo "🔗 **Related Commands:**"
echo "• /wtb:audit-project --scope state - Audit current state"
echo "• cat $STATE_FILE - View complete state"
echo "• /wtb:approve-decision - Process pending decisions"
echo

echo "🏷️ **WTB Namespace**: Collision-free state management"
```