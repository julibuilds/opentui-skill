---
name: opentui
description: This skill should be used when the user asks to "build a TUI", "create a terminal UI", "create TUI app", "use OpenTUI", or mentions OpenTUI, terminal user interface development, TUI components, keyboard handling in terminal apps, or terminal layout.
---

# OpenTUI Platform Skill

Consolidated skill for building terminal user interfaces with OpenTUI. Use decision trees below to find the right framework and components, then load detailed references.

## Critical Rules

**Follow these rules in all OpenTUI code:**

1. **Use `create-tui` for new projects.** See framework `REFERENCE.md` quick starts.
2. **`create-tui` options must come before arguments.** `bunx create-tui -t react my-app` works, `bunx create-tui my-app -t react` does NOT.
3. **Never call `process.exit()` directly.** Use `renderer.destroy()` (see `core/gotchas.md`).
4. **Text styling requires nested tags in React.** Use modifier elements, not props (see `components/text-display.md`).

## How to Use This Skill

### Reference File Structure

Framework references follow a 5-file pattern. Cross-cutting concepts are single-file guides.

Each framework in `./references/<framework>/` contains:

| File | Purpose | When to Read |
|------|---------|--------------|
| `REFERENCE.md` | Overview, when to use, quick start | **Always read first** |
| `api.md` | Runtime API, components, hooks | Writing code |
| `configuration.md` | Setup, tsconfig, bundling | Configuring a project |
| `patterns.md` | Common patterns, best practices | Implementation guidance |
| `gotchas.md` | Pitfalls, limitations, debugging | Troubleshooting |

Cross-cutting concepts in `./references/<concept>/` have `REFERENCE.md` as the entry point.

### Reading Order

1. Start with `REFERENCE.md` for your chosen framework
2. Then read additional files relevant to your task:
   - Building components -> `api.md` + `components/<category>.md`
   - Setting up project -> `configuration.md`
   - Layout/positioning -> `layout/REFERENCE.md`
   - Troubleshooting -> `gotchas.md` + `testing/REFERENCE.md`

### Example Paths

```
./references/react/REFERENCE.md           # Start here for React
./references/react/api.md              # React components and hooks
./references/components/inputs.md      # Input, Textarea, Select docs
./references/core/gotchas.md           # Core debugging tips
```

### Runtime Notes

OpenTUI runs on Bun and uses Zig for native builds. Read `./references/core/gotchas.md` for runtime requirements and build guidance.

## Quick Decision Trees

### "Which framework should I use?"

```
Which framework?
├─ I want full control, maximum performance, no framework overhead
│  └─ core/ (imperative API)
├─ I know React, want familiar component patterns
│  └─ react/ (React reconciler)
└─ I'm building a library/framework on top of OpenTUI
   └─ core/ (imperative API)
```

### "I need to display content"

```
Display content?
├─ Plain or styled text -> components/text-display.md
├─ Container with borders/background -> components/containers.md
├─ Scrollable content area -> components/containers.md (scrollbox)
├─ ASCII art banner/title -> components/text-display.md (ascii-font)
├─ Code with syntax highlighting -> components/code-diff.md
├─ Diff viewer (unified/split) -> components/code-diff.md
└─ Line numbers with diagnostics -> components/code-diff.md
```

### "I need user input"

```
User input?
├─ Single-line text field -> components/inputs.md (input)
├─ Multi-line text editor -> components/inputs.md (textarea)
├─ Select from a list (vertical) -> components/inputs.md (select)
├─ Tab-based selection (horizontal) -> components/inputs.md (tab-select)
└─ Custom keyboard shortcuts -> keyboard/REFERENCE.md
```

### "I need layout/positioning"

```
Layout?
├─ Flexbox-style layouts (row, column, wrap) -> layout/REFERENCE.md
├─ Absolute positioning -> layout/patterns.md
├─ Responsive to terminal size -> layout/patterns.md
├─ Centering content -> layout/patterns.md
└─ Complex nested layouts -> layout/patterns.md
```

### "I need animations"

```
Animations?
├─ Timeline-based animations -> animation/REFERENCE.md
├─ Easing functions -> animation/REFERENCE.md
├─ Property transitions -> animation/REFERENCE.md
└─ Looping animations -> animation/REFERENCE.md
```

### "I need to handle input"

```
Input handling?
├─ Keyboard events (keypress, release) -> keyboard/REFERENCE.md
├─ Focus management -> keyboard/REFERENCE.md
├─ Paste events -> keyboard/REFERENCE.md
├─ Mouse events -> components/containers.md
└─ Text selection -> components/text-display.md
```

### "I need to test my TUI"

```
Testing?
├─ Snapshot testing -> testing/REFERENCE.md
├─ Interaction testing -> testing/REFERENCE.md
├─ Test renderer setup -> testing/REFERENCE.md
└─ Debugging tests -> testing/REFERENCE.md
```

### "I need to debug/troubleshoot"

```
Troubleshooting?
├─ Runtime errors, crashes -> <framework>/gotchas.md
├─ Layout issues -> layout/REFERENCE.md + layout/patterns.md
├─ Input/focus issues -> keyboard/REFERENCE.md
└─ Repro + regression tests -> testing/REFERENCE.md
```

### Troubleshooting Index

- Terminal cleanup, crashes -> `core/gotchas.md`
- Text styling not applying -> `components/text-display.md`
- Input focus/shortcuts -> `keyboard/REFERENCE.md`
- Layout misalignment -> `layout/REFERENCE.md`
- Flaky snapshots -> `testing/REFERENCE.md`

For component naming differences and text modifiers, see `components/REFERENCE.md`.

## Product Index

### Frameworks
| Framework | Entry File | Description |
|-----------|------------|-------------|
| Core | `./references/core/REFERENCE.md` | Imperative API, all primitives |
| React | `./references/react/REFERENCE.md` | React reconciler for declarative TUI |

### Cross-Cutting Concepts
| Concept | Entry File | Description |
|---------|------------|-------------|
| Layout | `./references/layout/REFERENCE.md` | Yoga/Flexbox layout system |
| Components | `./references/components/REFERENCE.md` | Component reference by category |
| Keyboard | `./references/keyboard/REFERENCE.md` | Keyboard input handling |
| Animation | `./references/animation/REFERENCE.md` | Timeline-based animations |
| Testing | `./references/testing/REFERENCE.md` | Test renderer and snapshots |

### Component Categories
| Category | Entry File | Components |
|----------|------------|------------|
| Text & Display | `./references/components/text-display.md` | text, ascii-font, styled text |
| Containers | `./references/components/containers.md` | box, scrollbox, borders |
| Inputs | `./references/components/inputs.md` | input, textarea, select, tab-select |
| Code & Diff | `./references/components/code-diff.md` | code, line-number, diff |

## Task Execution Workflow

When invoked with `/opentui <task>`, follow this workflow:

### Step 1: Identify task type

Analyze $ARGUMENTS to determine:
- **Framework needed**: Core (imperative) or React (declarative)
- **Task type**: new project setup, component implementation, layout, keyboard handling, debugging, testing

Use decision trees above to select correct reference files.

### Step 2: Read relevant reference files

Based on task type, read from `./references/<area>/`:

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

### Step 3: Execute task

Apply OpenTUI-specific patterns and APIs from references to complete the user's request.

### Step 4: Summarize

```
=== OpenTUI Task Complete ===

Framework: <core | react>
Files referenced: <reference files consulted>

<brief summary of what was done>
```

<user-request>
$ARGUMENTS
</user-request>

## Resources

**Repository**: https://github.com/anomalyco/opentui
**Core Docs**: https://github.com/anomalyco/opentui/tree/main/packages/core/docs
**Examples**: https://github.com/anomalyco/opentui/tree/main/packages/core/src/examples
**Awesome List**: https://github.com/msmps/awesome-opentui
