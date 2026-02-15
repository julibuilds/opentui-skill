# Code & Diff Components

Components for displaying code with syntax highlighting and diffs in OpenTUI.

## Code Component

Display syntax-highlighted code blocks using Tree-sitter.

### Basic Usage

```tsx
// React
<code
  content={`function hello() {
  console.log("Hello, World!");
}`}
  filetype="typescript"
  syntaxStyle={syntaxStyle}
/>

// Core
import { CodeRenderable, SyntaxStyle, RGBA } from "@opentui/core"

const codeBlock = new CodeRenderable(renderer, {
  id: "code",
  content: sourceCode,
  filetype: "typescript",
  syntaxStyle: mySyntaxStyle,
})
```

### SyntaxStyle

A `SyntaxStyle` is required for syntax highlighting. Create one with `SyntaxStyle.fromStyles()`:

```typescript
import { SyntaxStyle, RGBA } from "@opentui/core"

const syntaxStyle = SyntaxStyle.fromStyles({
  keyword: { fg: RGBA.fromHex("#ff6b6b"), bold: true },
  string: { fg: RGBA.fromHex("#51cf66") },
  comment: { fg: RGBA.fromHex("#868e96"), italic: true },
  number: { fg: RGBA.fromHex("#ffd43b") },
  default: { fg: RGBA.fromHex("#ffffff") },
})
```

### Props

| Prop | Type | Description |
|------|------|-------------|
| `content` | `string` | The code string to display |
| `filetype` | `string` | Language for syntax highlighting (e.g., `"typescript"`) |
| `syntaxStyle` | `SyntaxStyle` | Style definitions for syntax tokens |
| `conceal` | `boolean` | Enable text concealing (default: `true`) |
| `drawUnstyledText` | `boolean` | Show text before highlighting completes |

### Supported Languages

OpenTUI uses Tree-sitter for syntax highlighting. Common filetypes:
- `typescript`, `javascript`
- `python`
- `rust`
- `go`
- `json`
- `html`, `css`
- `markdown`
- `bash`, `shell`

## Line Number Component

A **container component** that wraps a child (typically `<code>`) and adds a line number gutter with optional line coloring, signs, and diagnostics.

### Basic Usage

```tsx
// React - line-number wraps a code child
import { RGBA, SyntaxStyle } from "@opentui/core"

<line-number
  fg="#6b7280"
  bg="#161b22"
  minWidth={3}
  paddingRight={1}
  showLineNumbers
>
  <code
    content={sourceCode}
    filetype="typescript"
    syntaxStyle={syntaxStyle}
  />
</line-number>

// Core
const lineNum = new LineNumberRenderable(renderer, {
  id: "line-num",
  fg: "#6b7280",
  bg: "#161b22",
  minWidth: 3,
  paddingRight: 1,
  showLineNumbers: true,
})
const code = new CodeRenderable(renderer, {
  id: "code",
  content: sourceCode,
  filetype: "typescript",
  syntaxStyle: mySyntaxStyle,
})
lineNum.add(code)
```

### Props

| Prop | Type | Description |
|------|------|-------------|
| `fg` | `string \| RGBA` | Line number foreground color |
| `bg` | `string \| RGBA` | Line number background color |
| `minWidth` | `number` | Minimum gutter width |
| `paddingRight` | `number` | Padding between gutter and content |
| `showLineNumbers` | `boolean` | Show/hide line numbers |
| `lineNumberOffset` | `number` | Offset for line numbering |

### Ref-based API (React)

Use refs to set line colors and signs dynamically:

```tsx
import type { LineNumberRenderable } from "@opentui/core"
import { useEffect, useRef } from "react"

function CodeWithAnnotations({ code, syntaxStyle }) {
  const lineNumberRef = useRef<LineNumberRenderable>(null)

  useEffect(() => {
    // Highlight a line with a background color
    lineNumberRef.current?.setLineColor(1, "#1a4d1a")

    // Add a sign (icon/marker) to a line
    lineNumberRef.current?.setLineSign(1, {
      after: " +",
      afterColor: "#22c55e",
    })

    // Add a diagnostic indicator
    lineNumberRef.current?.setLineSign(4, {
      before: "⚠️",
      beforeColor: "#f59e0b",
    })
  }, [])

  return (
    <line-number ref={lineNumberRef} fg="#6b7280" bg="#161b22" showLineNumbers>
      <code content={code} filetype="typescript" syntaxStyle={syntaxStyle} />
    </line-number>
  )
}
```

### Imperative Methods

| Method | Description |
|--------|-------------|
| `setLineColor(line, color)` | Set background color for a line |
| `clearLineColor(line)` | Remove line color |
| `clearAllLineColors()` | Remove all line colors |
| `setLineColors(map)` | Set multiple line colors at once |
| `setLineSign(line, sign)` | Set a sign (before/after marker) on a line |
| `clearLineSign(line)` | Remove line sign |
| `clearAllLineSigns()` | Remove all line signs |

### LineSign Interface

```typescript
interface LineSign {
  before?: string       // Text before line number
  beforeColor?: string | RGBA
  after?: string        // Text after line number
  afterColor?: string | RGBA
}
```

## Diff Component

Unified or split diff viewer with syntax highlighting. Accepts a **unified diff string** (the standard format produced by `diff` tools and Git).

### Basic Usage

```tsx
// React
<diff
  diff={unifiedDiffString}
  filetype="typescript"
  syntaxStyle={syntaxStyle}
  view="unified"
  showLineNumbers
/>

// Core
const diffView = new DiffRenderable(renderer, {
  id: "diff",
  diff: unifiedDiffString,
  filetype: "typescript",
  syntaxStyle: mySyntaxStyle,
  view: "unified",
  showLineNumbers: true,
})
```

### Props

| Prop | Type | Description |
|------|------|-------------|
| `diff` | `string` | Unified diff string |
| `view` | `"unified" \| "split"` | Display mode (default: `"unified"`) |
| `filetype` | `string` | Language for syntax highlighting |
| `syntaxStyle` | `SyntaxStyle` | Syntax highlighting style |
| `showLineNumbers` | `boolean` | Show line numbers |
| `wrapMode` | `"word" \| "char" \| "none"` | Text wrapping mode |

### Diff Styling

```tsx
<diff
  diff={diffString}
  filetype="typescript"
  syntaxStyle={syntaxStyle}
  addedBg="#2d4f2d"
  removedBg="#4f2d2d"
  contextBg="transparent"
  addedSignColor="#22c55e"
  removedSignColor="#ef4444"
  lineNumberFg="#6b7280"
  lineNumberBg="#161b22"
/>
```

| Styling Prop | Description |
|-------------|-------------|
| `addedBg` | Background for added lines |
| `removedBg` | Background for removed lines |
| `contextBg` | Background for context lines |
| `addedContentBg` | Content area background for added lines |
| `removedContentBg` | Content area background for removed lines |
| `addedSignColor` | Color for the `+` sign |
| `removedSignColor` | Color for the `-` sign |
| `lineNumberFg` | Line number foreground |
| `lineNumberBg` | Line number background |

## Use Cases

### Code Editor

```tsx
function CodeEditor({ syntaxStyle }) {
  return (
    <box flexDirection="column" height="100%">
      <box height={1}>
        <text>editor.ts</text>
      </box>
      <textarea
        initialValue={`function hello() {\n  console.log("Hello!");\n}`}
        syntaxStyle={syntaxStyle}
        onContentChange={(event) => console.log("Changed")}
        flexGrow={1}
        focused
      />
    </box>
  )
}
```

### Code Review

```tsx
function CodeReview({ diffString, syntaxStyle }) {
  return (
    <box flexDirection="column" height="100%">
      <box height={1} backgroundColor="#333">
        <text>Changes in src/utils.ts</text>
      </box>
      <diff
        diff={diffString}
        filetype="typescript"
        syntaxStyle={syntaxStyle}
        view="split"
        showLineNumbers
      />
    </box>
  )
}
```

### Syntax-Highlighted Preview

```tsx
function CodePreview({ blocks, syntaxStyle }) {
  return (
    <scrollbox height={20}>
      {blocks.map((block, i) => (
        <box key={i} marginBottom={1}>
          <code
            content={block.content}
            filetype={block.filetype}
            syntaxStyle={syntaxStyle}
          />
        </box>
      ))}
    </scrollbox>
  )
}
```

### Annotated Code View

```tsx
function AnnotatedCode({ code, syntaxStyle }) {
  const ref = useRef<LineNumberRenderable>(null)

  useEffect(() => {
    ref.current?.setLineColor(3, "#1a4d1a")
    ref.current?.setLineSign(3, { before: "⚠️", beforeColor: "#f59e0b" })
  }, [])

  return (
    <line-number ref={ref} fg="#6b7280" bg="#161b22" showLineNumbers>
      <code content={code} filetype="typescript" syntaxStyle={syntaxStyle} />
    </line-number>
  )
}
```

## Gotchas

### Filetype Required for Highlighting

```tsx
// No highlighting (plain text)
<code content={text} syntaxStyle={syntaxStyle} />

// With highlighting
<code content={text} filetype="typescript" syntaxStyle={syntaxStyle} />
```

### LineNumber is a Container

`<line-number>` wraps a child component — it does not accept `content` or `filetype` props directly. Place a `<code>` component inside it.

```tsx
// WRONG
<line-number content={code} filetype="typescript" />

// CORRECT
<line-number showLineNumbers>
  <code content={code} filetype="typescript" syntaxStyle={syntaxStyle} />
</line-number>
```

### Diff Takes a Unified Diff String

The `<diff>` component accepts a single `diff` prop containing a unified diff string (the format produced by `git diff` or the `diff` npm package), not separate old/new code strings.

### Large Files

For very large files, consider:
- Pagination or virtual scrolling
- Loading only visible portion
- Using `scrollbox` wrapper

```tsx
<scrollbox height={30}>
  <line-number showLineNumbers>
    <code content={largeFile} filetype="typescript" syntaxStyle={syntaxStyle} />
  </line-number>
</scrollbox>
```

### Tree-sitter Loading

Syntax highlighting requires Tree-sitter grammars. If highlighting isn't working:

1. Check the filetype is supported
2. Verify grammars are installed
3. Check `OTUI_TREE_SITTER_WORKER_PATH` if using custom path
