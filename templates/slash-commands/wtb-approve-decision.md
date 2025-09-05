---
description: "Process pending decisions from approval workflow"
argument-hint: "[decision-id] [--action approve|reject] [--comment]"
allowed-tools: [Read, Write, Bash, LS]
---

Process pending decisions in the approval workflow with audit logging.

**Usage**: `/wtb:approve-decision [decision-id] [--action approve|reject] [--comment]`

**Parameters:**
- `decision-id`: ID of pending decision to process
- `--action`: Action to take (approve|reject)
- `--comment`: Optional comment explaining the decision

**What this command does:**
1. **Validation**: Checks decision exists and is pending
2. **Processing**: Applies approval or rejection
3. **Logging**: Records decision with timestamp and rationale
4. **Notification**: Updates project state and related systems
5. **Cleanup**: Moves decision from pending to completed

---

```bash
#!/bin/bash
source "$(dirname "$0")/_shared-utils.md"
set_shell_safety

# Parse arguments
DECISION_ID="${1:-}"
ACTION=""
COMMENT=""

for arg in "$@"; do
    case $arg in
        --action=*) ACTION="${arg#*=}" ;;
        --comment=*) COMMENT="${arg#*=}" ;;
    esac
done

# Validate required arguments
if [[ -z "$DECISION_ID" ]] || [[ -z "$ACTION" ]]; then
    echo "❌ Missing required arguments: decision-id and --action"
    echo "Usage: /wtb:approve-decision dec-123 --action approve --comment 'Looks good'"
    exit 1
fi

if [[ ! "$ACTION" =~ ^(approve|reject)$ ]]; then
    echo "❌ Invalid action. Use: approve or reject"
    exit 1
fi

generate_report_header "WTB Decision Processor" "🏷️"

echo "📋 Configuration:"
echo "  🆔 Decision ID: $DECISION_ID"
echo "  ⚡ Action: $ACTION"
echo "  💬 Comment: ${COMMENT:-'(none)'}"
echo

STATE_FILE=".claude/project-state.json"

# Check if state file exists
if [[ ! -f "$STATE_FILE" ]]; then
    echo "❌ State file not found: $STATE_FILE"
    echo "💡 Initialize project first: /wtb:init-project"
    exit 1
fi

# Process decision using Python for safe JSON handling
python3 << PYTHON_SCRIPT
import json
import sys
from datetime import datetime

state_file = "$STATE_FILE"
decision_id = "$DECISION_ID"
action = "$ACTION"
comment = "$COMMENT"

try:
    # Load current state
    with open(state_file, 'r') as f:
        state = json.load(f)
    
    # Find pending decision
    pending = state.get('workflow', {}).get('pending_decisions', [])
    decision_found = None
    
    for i, decision in enumerate(pending):
        if decision.get('id') == decision_id:
            decision_found = pending.pop(i)
            break
    
    if not decision_found:
        print(f"❌ Decision {decision_id} not found in pending decisions")
        sys.exit(1)
    
    # Process decision
    decision_found.update({
        'status': action,
        'processed_at': datetime.utcnow().isoformat() + 'Z',
        'comment': comment,
        'processed_by': 'wtb:approve-decision'
    })
    
    # Move to completed decisions
    if 'completed_decisions' not in state['workflow']:
        state['workflow']['completed_decisions'] = []
    
    state['workflow']['completed_decisions'].append(decision_found)
    
    # Add to change log
    if 'change_log' not in state:
        state['change_log'] = []
    
    state['change_log'].append({
        'timestamp': datetime.utcnow().isoformat() + 'Z',
        'action': f'decision_{action}',
        'decision_id': decision_id,
        'comment': comment,
        'changed_by': 'wtb:approve-decision'
    })
    
    # Write updated state
    with open(state_file, 'w') as f:
        json.dump(state, f, indent=2)
    
    print(f"  ✅ Decision {decision_id} {action}d successfully")
    print(f"  📝 Decision: {decision_found.get('title', 'N/A')}")
    if comment:
        print(f"  💬 Comment: {comment}")
    
except Exception as e:
    print(f"❌ Failed to process decision: {e}")
    sys.exit(1)
PYTHON_SCRIPT

echo
echo "✅ WTB Decision Processing Complete!"
echo
echo "🔗 **Related Commands:**"
echo "• /wtb:audit-project --scope decisions - Review all decisions"
echo "• cat $STATE_FILE - View complete state"
echo "• /wtb:update-state - Modify project state"
echo

echo "🏷️ **WTB Namespace**: Collision-free workflow management"
```