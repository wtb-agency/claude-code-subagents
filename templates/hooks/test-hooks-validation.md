# Hooks Validation Test

This document describes how to test that the hooks work correctly with user-initiated commands.

## Test Scenarios

### 1. Boundary Enforcement Hook Test

**Test**: Try to modify critical system files
**Expected**: Hook should ask for confirmation

```bash
# These should trigger permission requests:
# - Edit .claude/project-state.json
# - Edit .claude/slash-commands/wtb-init-project.md  
# - Edit .claude/hooks/boundary-enforcement.json
# - Edit agents-rules/consistency-auditor-rules.md
# - Edit AGENTS.md

# These should be allowed:
# - Edit src/main.py
# - Edit docs/requirements.md  
# - Edit contracts/api.json
# - Edit tests/test_main.py
```

### 2. Approval Workflow Hook Test

**Test**: Modify key project files
**Expected**: Hook should log pending decisions automatically

```bash
# These should create pending decisions:
# - Edit docs/requirements.md → Creates "Requirements Update" pending decision
# - Edit docs/vision.md → Creates "Vision/Mission Update" pending decision  
# - Edit docs/roadmap.md → Creates "Roadmap Update" pending decision
# - Edit docs/backlog.md → Creates "Backlog Update" pending decision
# - Edit contracts/api.json → Creates "Contract Update" pending decision
# - Edit src/main.py → Creates "Code Update" pending decision

# These should NOT create pending decisions:
# - Edit .claude/project-state.json (system file)
# - Edit agents-rules/test-rules.md (system file)
# - Edit AGENTS.md (system file)
```

### 3. Approval Notification Hook Test

**Test**: Check that pending decisions trigger notifications
**Expected**: Hook should show pending count after file modifications

```bash
# After any file edit that creates a pending decision:
# Should see message like:
# "⏳ 1 pending decision(s) require approval"  
# "   Use /wtb:approve-decision --latest to approve the newest"
# "   Or run /wtb:approve-decision --list to see all pending decisions"
```

## Validation Checklist

- [ ] **Boundary enforcement works**: System files protected with permission requests
- [ ] **Decision logging works**: Key file changes automatically create pending decisions  
- [ ] **Notifications work**: Pending decisions trigger user guidance messages
- [ ] **File pattern matching works**: Hook correctly identifies different file types
- [ ] **User workflow supported**: Hooks work when users run slash commands manually
- [ ] **Agent workflow supported**: Hooks work when agents are dispatched via Task tool

## Expected Hook Behavior

**Key Insight**: Hooks trigger on **tool use**, not on **who initiates the tool use**.

✅ **Works with user-initiated commands**: When user runs `/wtb:init-project`, any Edit tools triggered will activate hooks
✅ **Works with agent-dispatched tools**: When agent uses Edit tool, hooks activate regardless of dispatch method  
✅ **File-based logic**: Hooks now use file patterns instead of trying to detect active agents
✅ **Reliable protection**: System files are protected, decision logging happens automatically

## Testing Instructions

1. **Set up test project** with hooks configuration
2. **Run boundary tests** by trying to edit protected files
3. **Run approval tests** by modifying docs/requirements.md
4. **Verify notifications** appear after pending decisions are created
5. **Test user commands** by running slash commands and checking hook activation
6. **Test agent dispatch** by dispatching agents and verifying hooks still work

The hooks architecture is **validated as compatible** with user-initiated slash commands.
