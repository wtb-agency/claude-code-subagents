---
description: "Process pending decisions from approval workflow"
argument-hint: "[--latest] [--list] [--action approve|reject] [--comment]"
allowed-tools: [Read, Write, Bash, LS]
---

Process pending decisions in the approval workflow with audit logging.

**Usage**: `/wtb:approve-decision [--latest] [--list] [--action approve|reject] [--comment]`

**Parameters:**
- `--latest`: Approve/reject the most recent pending decision
- `--list`: List all pending decisions and exit
- `--action`: Action to take (approve|reject) — defaults to approve if omitted
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

ACTION="approve"
COMMENT=""
MODE="latest"

for arg in "$@"; do
    case $arg in
        --action=*) ACTION="${arg#*=}" ;;
        --comment=*) COMMENT="${arg#*=}" ;;
        --latest) MODE="latest" ;;
        --list) MODE="list" ;;
    esac
done

if [[ ! "$ACTION" =~ ^(approve|reject)$ ]]; then
    echo "❌ Invalid action. Use: approve or reject"
    exit 1
fi

generate_report_header "WTB Decision Processor" "🏷️"

echo "📋 Configuration:"
echo "  🧭 Mode: $MODE"
echo "  ⚡ Action: $ACTION"
echo "  💬 Comment: ${COMMENT:-'(none)'}"
echo

DECISIONS_FILE="docs/decisions.md"
if [[ ! -f "$DECISIONS_FILE" ]]; then
  echo "❌ Decisions log not found: $DECISIONS_FILE"
  echo "💡 It will be created automatically when hooks log a decision."
  exit 1
fi

python3 << 'PYTHON_SCRIPT'
import re
import sys
from datetime import datetime, timezone

MODE = """%s"""
ACTION = """%s"""
COMMENT = """%s"""
DECISIONS_FILE = "docs/decisions.md"

with open(DECISIONS_FILE, 'r') as f:
    lines = f.readlines()

# Parse decision blocks starting with '## '
blocks = []
start = None
for i, line in enumerate(lines):
    if line.startswith('## '):
        if start is not None:
            blocks.append((start, i))
        start = i
if start is not None:
    blocks.append((start, len(lines)))

def status_of(block):
    for l in lines[block[0]:block[1]]:
        m = re.search(r"\*\*Status\*\*:\s*(\w+)", l)
        if m:
            return m.group(1).strip()
    return None

pending = [b for b in blocks if status_of(b) == 'Pending']

if MODE == 'list':
    if not pending:
        print('✅ No pending decisions')
        sys.exit(0)
    print('📋 Pending decisions:')
    for idx, (s, e) in enumerate(pending, 1):
        header = lines[s].strip().lstrip('#').strip()
        print(f"  {idx}. {header}")
    sys.exit(0)

if not pending:
    print('✅ No pending decisions to process')
    sys.exit(0)

target = pending[-1]  # latest pending
s, e = target

# Update status line and add processed/comment lines after it if not present
processed_ts = datetime.now(timezone.utc).isoformat(timespec='seconds').replace('+00:00','Z')
new_chunk = []
inserted_meta = False
for l in lines[s:e]:
    if not inserted_meta and re.search(r"\*\*Status\*\*:\s*Pending", l):
        new_chunk.append(f"**Status**: {'Approved' if ACTION=='approve' else 'Rejected'}\n")
        new_chunk.append(f"**Processed**: {processed_ts}\n")
        if COMMENT:
            new_chunk.append(f"**Comment**: {COMMENT}\n")
        inserted_meta = True
    else:
        new_chunk.append(l)

lines[s:e] = new_chunk

with open(DECISIONS_FILE, 'w') as f:
    f.writelines(lines)

print(f"  ✅ Decision {('approved' if ACTION=='approve' else 'rejected')} successfully")
print('  📝 Updated decisions log: docs/decisions.md')
PYTHON_SCRIPT

echo
echo "✅ WTB Decision Processing Complete!"
echo
echo "🔗 **Related Commands:**"
echo "• /wtb:audit-project --scope decisions - Review all decisions"
echo "• cat docs/decisions.md - View decisions log"
echo "• /wtb:update-state - Modify project state"
echo

echo "🏷️ **WTB Namespace**: Collision-free workflow management"
```
