# React API Reference

## Rendering

### createRoot(renderer)

Creates a React root for rendering.

```tsx
import { createCliRenderer } from "@opentui/core"
import { createRoot } from "@opentui/react"

const renderer = await createCliRenderer({
  exitOnCtrlC: false,  // Handle Ctrl+C yourself
})

const root = createRoot(renderer)
root.render(<App />)
```

## Hooks

### useRenderer()

Access the OpenTUI renderer instance.

```tsx
import { useRenderer } from "@opentui/react"
import { useEffect } from "react"

function App() {
  const renderer = useRenderer()
  
  useEffect(() => {
    // Access renderer properties
    console.log(`Terminal: ${renderer.width}x${renderer.height}`)
    
    // Show debug console
    renderer.console.show()
  }, [renderer])
  
  return <text>Hello</text>
}
```

### useKeyboard(handler, options?)

Handle keyboard events.

```tsx
import { useKeyboard, useRenderer } from "@opentui/react"

function App() {
  const renderer = useRenderer()
  
  useKeyboard((key) => {
    if (key.name === "escape") {
      renderer.destroy()  // Never use process.exit() directly!
    }
    if (key.ctrl && key.name === "s") {
      saveDocument()
    }
  })
  
  return <text>Press ESC to exit</text>
}

// With release events
function GameControls() {
  const [pressed, setPressed] = useState(new Set<string>())
  
  useKeyboard(
    (event) => {
      setPressed(keys => {
        const newKeys = new Set(keys)
        if (event.eventType === "release") {
          newKeys.delete(event.name)
        } else {
          newKeys.add(event.name)
        }
        return newKeys
      })
    },
    { release: true }  // Include release events
  )
  
  return <text>Pressed: {Array.from(pressed).join(", ")}</text>
}
```

**Options:**
- `release?: boolean` - Include key release events (default: false)

**KeyEvent properties:**
- `name: string` - Key name ("a", "escape", "f1", etc.)
- `sequence: string` - Raw escape sequence
- `ctrl: boolean` - Ctrl modifier
- `shift: boolean` - Shift modifier
- `meta: boolean` - Alt modifier
- `option: boolean` - Option modifier (macOS)
- `eventType: "press" | "release" | "repeat"`
- `repeated: boolean` - Key is being held

### useOnResize(callback)

Handle terminal resize events.

```tsx
import { useOnResize } from "@opentui/react"

function App() {
  useOnResize((width, height) => {
    console.log(`Resized to ${width}x${height}`)
  })
  
  return <text>Resize the terminal</text>
}
```

### useTerminalDimensions()

Get reactive terminal dimensions.

```tsx
import { useTerminalDimensions } from "@opentui/react"

function ResponsiveLayout() {
  const { width, height } = useTerminalDimensions()
  
  return (
    <box flexDirection={width > 80 ? "row" : "column"}>
      <box flexGrow={1}>
        <text>Width: {width}</text>
      </box>
      <box flexGrow={1}>
        <text>Height: {height}</text>
      </box>
    </box>
  )
}
```

### useTimeline(options?)

Create animations with the timeline system.

```tsx
import { useTimeline } from "@opentui/react"
import { useEffect, useState } from "react"

function AnimatedBox() {
  const [width, setWidth] = useState(0)
  
  const timeline = useTimeline({
    duration: 2000,
    loop: false,
  })
  
  useEffect(() => {
    timeline.add(
      { width: 0 },
      {
        width: 50,
        duration: 2000,
        ease: "easeOutQuad",
        onUpdate: (anim) => {
          setWidth(Math.round(anim.targets[0].width))
        },
      }
    )
  }, [timeline])
  
  return <box style={{ width, height: 3, backgroundColor: "#6a5acd" }} />
}
```

**Options:**
- `duration?: number` - Default duration (ms)
- `loop?: boolean` - Loop the timeline
- `autoplay?: boolean` - Auto-start (default: true)
- `onComplete?: () => void` - Completion callback
- `onPause?: () => void` - Pause callback

**Timeline methods:**
- `add(target, properties, startTime?)` - Add animation
- `play()` - Start playback
- `pause()` - Pause playback
- `restart()` - Restart from beginning

## Components

### Text Component

```tsx
<text
  content="Hello"           // Or use children
  fg="#FFFFFF"              // Foreground color
  bg="#000000"              // Background color
  selectable={true}         // Allow text selection
>
  {/* Use nested modifier tags for styling */}
  <span fg="red">Red</span>
  <strong>Bold</strong>
  <em>Italic</em>
  <u>Underline</u>
  <br />
  <a href="https://...">Link</a>
</text>
```

> **Note**: Do NOT use `bold`, `italic`, `underline` as props on `<text>`. Use nested modifier tags like `<strong>`, `<em>`, `<u>` instead.

### Input Component

```tsx
<input
  value={value}
  placeholder="Enter text..."
  focused
  width={30}
  backgroundColor="#1a1a1a"
  textColor="#FFFFFF"
  cursorColor="#00FF00"
  focusedBackgroundColor="#2a2a2a"
  onInput={(value) => setValue(value)}
  onChange={(value) => console.log("Committed:", value)}
  onSubmit={(value) => handleSubmit(value)}
/>
```

**Event props:**

| Prop | Fires When | Use Case |
|------|-----------|----------|
| `onInput` | Every edit action (typing, paste, delete) | Update state on each keystroke |
| `onChange` | Blur or Enter (committed value) | Validate on commit |
| `onSubmit` | Enter key pressed | Submit form |

### Box Component

```tsx
<box
  // Borders
  border                    // Enable border
  borderStyle="single"      // single | double | rounded | bold
  borderColor="#FFFFFF"
  title="Title"
  titleAlignment="center"   // left | center | right
  
  // Colors
  backgroundColor="#1a1a2e"
  
  // Layout (see layout/REFERENCE.md)
  flexDirection="row"
  justifyContent="center"
  alignItems="center"
  gap={2}
  
  // Spacing
  padding={2}
  paddingTop={1}
  margin={1}
  
  // Dimensions
  width={40}
  height={10}
  flexGrow={1}
  
  // Events
  onMouseDown={(e) => {}}
  onMouseUp={(e) => {}}
  onMouseMove={(e) => {}}
>
  {children}
</box>
```

### Scrollbox Component

```tsx
<scrollbox
  focused                   // Enable keyboard scrolling
  style={{
    rootOptions: { backgroundColor: "#24283b" },
    wrapperOptions: { backgroundColor: "#1f2335" },
    viewportOptions: { backgroundColor: "#1a1b26" },
    contentOptions: { backgroundColor: "#16161e" },
    scrollbarOptions: {
      showArrows: true,
      trackOptions: {
        foregroundColor: "#7aa2f7",
        backgroundColor: "#414868",
      },
    },
  }}
>
  {/* Scrollable content */}
  {items.map((item, i) => (
    <box key={i}>
      <text>{item}</text>
    </box>
  ))}
</scrollbox>
```

### Textarea Component

```tsx
<textarea
  initialValue={text}
  onContentChange={(event) => handleChange(event)}
  placeholder="Enter multiple lines..."
  focused
  width={40}
  height={10}
  wrapMode="word"
/>
```

### Select Component

```tsx
<select
  options={[
    { name: "Option 1", description: "First option", value: "1" },
    { name: "Option 2", description: "Second option", value: "2" },
  ]}
  onChange={(index, option) => setSelected(option)}
  selectedIndex={0}
  focused
  showScrollIndicator
  height={8}
/>
```

### Tab Select Component

```tsx
<tab-select
  options={[
    { name: "Home", description: "Dashboard" },
    { name: "Settings", description: "Configuration" },
  ]}
  onChange={(index, option) => setTab(option)}
  tabWidth={20}
  focused
/>
```

### ASCII Font Component

```tsx
<ascii-font
  text="TITLE"
  font="tiny"               // tiny | block | slick | shade
  color="#FFFFFF"
/>
```

### Code Component

```tsx
import { SyntaxStyle, RGBA } from "@opentui/core"

const syntaxStyle = SyntaxStyle.fromStyles({
  keyword: { fg: RGBA.fromHex("#ff6b6b"), bold: true },
  string: { fg: RGBA.fromHex("#51cf66") },
  comment: { fg: RGBA.fromHex("#868e96"), italic: true },
  number: { fg: RGBA.fromHex("#ffd43b") },
  default: { fg: RGBA.fromHex("#ffffff") },
})

<code
  content={sourceCode}
  filetype="typescript"
  syntaxStyle={syntaxStyle}
/>
```

**Props:** `content` (code string), `filetype` (language for highlighting), `syntaxStyle` (SyntaxStyle instance), `conceal` (boolean), `drawUnstyledText` (boolean).

### Line Number Component

A **container** that wraps a `<code>` child and adds a line number gutter. Use refs for line colors and signs.

```tsx
import type { LineNumberRenderable } from "@opentui/core"
import { useRef, useEffect } from "react"

const lineNumberRef = useRef<LineNumberRenderable>(null)

useEffect(() => {
  lineNumberRef.current?.setLineColor(1, "#1a4d1a")
  lineNumberRef.current?.setLineSign(1, { after: " +", afterColor: "#22c55e" })
}, [])

<line-number ref={lineNumberRef} fg="#6b7280" bg="#161b22" showLineNumbers>
  <code content={sourceCode} filetype="typescript" syntaxStyle={syntaxStyle} />
</line-number>
```

**Props:** `fg`, `bg`, `minWidth`, `paddingRight`, `showLineNumbers`, `lineNumberOffset`, `focused`.

**Ref methods:** `setLineColor(line, color)`, `clearLineColor(line)`, `clearAllLineColors()`, `setLineColors(map)`, `setLineSign(line, sign)`, `clearLineSign(line)`, `clearAllLineSigns()`.

### Diff Component

Accepts a **unified diff string** (from `git diff` or diff tools), not separate old/new code.

```tsx
<diff
  diff={unifiedDiffString}
  filetype="typescript"
  syntaxStyle={syntaxStyle}
  view="unified"              // unified | split
  showLineNumbers
  addedBg="#2d4f2d"
  removedBg="#4f2d2d"
/>
```

**Props:** `diff` (unified diff string), `view` (`"unified"` | `"split"`), `filetype`, `syntaxStyle`, `showLineNumbers`, plus styling props: `addedBg`, `removedBg`, `contextBg`, `addedSignColor`, `removedSignColor`, `lineNumberFg`, `lineNumberBg`.

## Type Exports

```tsx
import type {
  // Component props
  TextProps,
  BoxProps,
  InputProps,
  TextareaProps,
  SelectProps,
  CodeProps,
  DiffProps,
  LineNumberProps,
  MarkdownProps,

  // Hook types
  KeyEvent,

  // From core
  CliRenderer,
} from "@opentui/react"
```
