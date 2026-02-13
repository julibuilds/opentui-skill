---
name: opentui
description: This skill should be used when the user asks to "build a TUI", "create a terminal UI", "create TUI app", "use OpenTUI", or mentions OpenTUI, terminal user interface development, TUI components, or keyboard handling in terminal apps.
---

Load the OpenTUI TUI framework skill and help with any terminal user interface development task.

## Workflow

### Step 1: Load opentui skill

Read the skill definition from the skill directory:
- Read `skill/opentui/SKILL.md` for the main skill overview and decision trees

### Step 2: Identify task type from user request

Analyze $ARGUMENTS to determine:
- **Framework needed** (Core imperative, React declarative)
- **Task type** (new project setup, component implementation, layout, keyboard handling, debugging, testing)

Use decision trees in SKILL.md to select correct reference files.

### Step 3: Read relevant reference files

Based on task type, read from `skill/opentui/references/<area>/`:

| Task | Files to Read |
|------|---------------|
| New project setup | `<framework>/REFERENCE.md` + `<framework>/configuration.md` |
| Implement components | `<framework>/api.md` + `components/<category>.md` |
| Layout/positioning | `layout/REFERENCE.md` + `layout/patterns.md` |
| Handle keyboard input | `keyboard/REFERENCE.md` |
| Add animations | `animation/REFERENCE.md` |
| Debug/troubleshoot | `<framework>/gotchas.md` + `testing/REFERENCE.md` |
| Write tests | `testing/REFERENCE.md` |
| Understand patterns | `<framework>/patterns.md` |

### Step 4: Execute task

Apply OpenTUI-specific patterns and APIs from references to complete the user's request.

### Step 5: Summarize

```
=== OpenTUI Task Complete ===

Framework: <core | react>
Files referenced: <reference files consulted>

<brief summary of what was done>
```

<user-request>
$ARGUMENTS
</user-request>
