# Issue Management Automation System

## Overview

This repository has an intelligent Issue Management automation system that automatically triages, categorizes, and responds to issues based on their content.

## Workflow Components

### 1. Issue Triage (`issue-triage` job)

**Triggers:** When a new issue is opened

**Automatic Actions:**
- Analyzes issue title and body for keywords
- Assigns category labels based on title
- Assigns priority labels based on title OR body content
- Creates labels if they don't exist
- Adds `needs-triage` status label

**Category Labels (Title Keywords):**
- 🐛 `bug` - Title contains "bug"
- 🎨 `epic` - Title contains "epic"  
- 🔧 `maintenance` - Title contains "maintenance"

**Priority Labels (Title OR Body, Highest Priority Wins):**
- 🔴 `priority-critical` - Contains: critical, urgent, production, outage
- 🟠 `priority-high` - Contains: important, high, blocking
- 🟡 `priority-medium` - Contains: medium, normal (or default if no keywords)
- 🟢 `priority-low` - Contains: low, nice-to-have, minor

### 2. Task Breakdown (`task-breakdown` job)

**Triggers:** When a new issue with "Epic" in the title is opened

**Automatic Actions:**
- Creates 4 sub-issues with the pattern: `[SUBTASK] [Original Title] - Task N: [Task Name]`
- Task 1: Requirements Analysis
- Task 2: Design and Architecture
- Task 3: Implementation
- Task 4: Testing and Documentation
- Each sub-issue links back with "Related to #[parent-number]"
- All sub-issues get `enhancement` and `needs-review` labels
- Parent issue updated with "## Epic Tasks" checklist

### 3. Auto-Response (`auto-response` job)

**Triggers:** After issue-triage completes for newly opened issues

**Automatic Actions:**

**First-Time Contributors:**
- Detects if author's first issue in this repository
- Adds `first-time-contributor` label
- Posts personalized welcome message

**Type-Specific Responses:**
- 🐛 **Bug Issues**: Posts "Bug Report Guidelines" comment
- 🎨 **Epic Issues**: Posts "Feature Request Process" comment
- 🔧 **Maintenance Issues**: Posts "Maintenance Guidelines" comment

**Milestone Assignment:**
- Automatically assigns milestone "v1.0.0" to high and critical priority issues
- Creates milestone if it doesn't exist

**Status Updates:**
- Removes `needs-triage` label
- Adds `needs-review` label

## Labels Created

### Category Labels
| Label | Color | Description |
|-------|-------|-------------|
| `bug` | ![#d73a4a](https://via.placeholder.com/10/d73a4a/000000?text=+) `#d73a4a` | Something isn't working |
| `enhancement` | ![#a2eeef](https://via.placeholder.com/10/a2eeef/000000?text=+) `#a2eeef` | New feature or request |
| `epic` | ![#7057ff](https://via.placeholder.com/10/7057ff/000000?text=+) `#7057ff` | Large feature requiring multiple sub-tasks |
| `maintenance` | ![#fbca04](https://via.placeholder.com/10/fbca04/000000?text=+) `#fbca04` | Maintenance and housekeeping tasks |

### Priority Labels
| Label | Color | Description |
|-------|-------|-------------|
| `priority-critical` | ![#b60205](https://via.placeholder.com/10/b60205/000000?text=+) `#b60205` | Critical priority issue |
| `priority-high` | ![#d93f0b](https://via.placeholder.com/10/d93f0b/000000?text=+) `#d93f0b` | High priority issue |
| `priority-medium` | ![#fbca04](https://via.placeholder.com/10/fbca04/000000?text=+) `#fbca04` | Medium priority issue |
| `priority-low` | ![#0e8a16](https://via.placeholder.com/10/0e8a16/000000?text=+) `#0e8a16` | Low priority issue |

### Status Labels
| Label | Color | Description |
|-------|-------|-------------|
| `needs-triage` | ![#ededed](https://via.placeholder.com/10/ededed/000000?text=+) `#ededed` | Needs to be reviewed by maintainers |
| `needs-review` | ![#fbca04](https://via.placeholder.com/10/fbca04/000000?text=+) `#fbca04` | Awaiting review from maintainers |
| `first-time-contributor` | ![#7057ff](https://via.placeholder.com/10/7057ff/000000?text=+) `#7057ff` | Issue created by first-time contributor |

## Issue Templates

Three professional templates guide users in creating well-structured issues:

1. **Bug Report** (`.github/ISSUE_TEMPLATE/bug_report.md`)
   - Structured sections for reproduction steps, environment details
   - Pre-filled with `bug` label

2. **Feature Request** (`.github/ISSUE_TEMPLATE/feature_request.md`)
   - Epic/feature template with problem statement, solution, benefits
   - Pre-filled with `epic` and `enhancement` labels

3. **Maintenance Report** (`.github/ISSUE_TEMPLATE/maintenance_report.md`)
   - Task template with type, effort estimation, dependencies
   - Pre-filled with `maintenance` label

## Example Workflows

### Example 1: Bug Report
**Issue Title:** "Bug: Login form validation not working"

**Automated Actions:**
1. ✅ Adds `bug` label (title contains "bug")
2. ✅ Adds `priority-high` label (body contains "high priority")
3. ✅ Adds `needs-triage` label
4. ✅ Detects first-time contributor (if applicable)
5. ✅ Posts "Bug Report Guidelines" comment
6. ✅ Sets milestone "v1.0.0" (high priority)
7. ✅ Changes label from `needs-triage` to `needs-review`

### Example 2: Epic Feature Request
**Issue Title:** "Epic: Redesign user dashboard interface"

**Automated Actions:**
1. ✅ Adds `epic` label (title contains "epic")
2. ✅ Adds `priority-high` label (body contains "high priority")
3. ✅ Adds `needs-triage` label
4. ✅ Creates 4 sub-issues with proper linking
5. ✅ Updates parent with "## Epic Tasks" checklist
6. ✅ Posts "Feature Request Process" comment
7. ✅ Sets milestone "v1.0.0" (high priority)
8. ✅ Changes label from `needs-triage` to `needs-review`

### Example 3: Maintenance Task
**Issue Title:** "Weekly maintenance cleanup and refactor"

**Automated Actions:**
1. ✅ Adds `maintenance` label (title contains "maintenance")
2. ✅ Adds `priority-medium` label (default, no high-priority keywords)
3. ✅ Adds `needs-triage` label
4. ✅ Posts "Maintenance Guidelines" comment
5. ✅ No milestone (only high/critical get milestones)
6. ✅ Changes label from `needs-triage` to `needs-review`

## Technical Details

### Workflow File
- **Location:** `.github/workflows/issue-automation.yml`
- **Trigger:** `issues: [opened, labeled]`
- **Permissions:** `issues: write`, `contents: read`

### Jobs & Dependencies
```
issue-triage (independent)
    ↓
auto-response (depends on issue-triage)

task-breakdown (independent, only for Epics)
```

### Error Handling
- Graceful fallback if labels already exist
- Try-catch blocks for milestone operations
- Comprehensive logging for debugging

## Benefits

✨ **Automated Triage** - Saves maintainer time by automatically categorizing issues
📊 **Consistent Organization** - All issues follow the same labeling convention
🎯 **Priority Management** - High-priority issues are automatically tracked in milestones
👥 **Welcoming Community** - First-time contributors receive personalized welcome
📋 **Epic Management** - Large features automatically broken down into manageable tasks
📖 **Clear Guidelines** - Context-aware responses help issue reporters provide better information

## Testing

The system has been tested with:
- ✅ Bug issues (high priority)
- ✅ Epic feature requests (with sub-task creation)
- ✅ Maintenance tasks (medium priority)
- ✅ First-time contributor detection
- ✅ Automatic label creation
- ✅ Milestone assignment
- ✅ Status transitions

## Maintenance

The workflow is self-maintaining:
- Labels are created automatically if missing
- Milestones are created automatically if needed
- No manual configuration required for new issue types

---

**Note:** This automation activates immediately when issues are opened. The workflow typically completes within 30-60 seconds.
