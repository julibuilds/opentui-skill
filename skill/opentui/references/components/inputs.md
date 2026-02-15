# Input Components

Components for user input in OpenTUI.

## Input Component

Single-line text input field.

### Basic Usage

```tsx
// React
<input
  value={value}
  onInput={(newValue) => setValue(newValue)}
  placeholder="Enter text..."
  focused
/>

// Core
const input = new InputRenderable(renderer, {
  id: "name",
  placeholder: "Enter text...",
})
input.on(InputRenderableEvents.INPUT, (value) => {
  console.log("Value:", value)
})
input.focus()
```

### Styling

```tsx
<input
  width={30}
  backgroundColor="#1a1a1a"
  textColor="#FFFFFF"
  cursorColor="#00FF00"
  focusedBackgroundColor="#2a2a2a"
  placeholderColor="#666666"
/>
```

### Events

**React event props:**

| Prop | Fires When | Use Case |
|------|-----------|----------|
| `onInput` | Every edit action (typing, paste, delete) | Update state on each keystroke |
| `onChange` | Blur or Enter (committed value) | Validate on commit |
| `onSubmit` | Enter key pressed | Submit form |

```tsx
// React
<input
  onInput={(value) => setValue(value)}
  onChange={(value) => console.log("Committed:", value)}
  onSubmit={(value) => handleSubmit(value)}
/>

// Core
input.on(InputRenderableEvents.INPUT, (value) => {
  // Fired for all mutating edit actions (typing, paste, delete, etc.)
})
input.on(InputRenderableEvents.CHANGE, (value) => {
  // Fired on blur or Enter (committed value)
})
input.on(InputRenderableEvents.ENTER, (value) => {
  // Fired when Enter key is pressed
})
```

### Controlled Input

```tsx
// React
function ControlledInput() {
  const [value, setValue] = useState("")

  return (
    <input
      value={value}
      onInput={setValue}
      focused
    />
  )
}

```

## Textarea Component

Multi-line text input field.

### Basic Usage

```tsx
// React
<textarea
  initialValue={text}
  onContentChange={(event) => handleChange(event)}
  placeholder="Enter multiple lines..."
  width={40}
  height={10}
  focused
/>

// Core
const textarea = new TextareaRenderable(renderer, {
  id: "editor",
  width: 40,
  height: 10,
  placeholder: "Enter text...",
})
```

### Features

```tsx
<textarea
  wrapMode="word"          // "none" | "char" | "word" (default: "word")
  showCursor={true}        // Show/hide cursor
  tabIndicator={2}         // Tab character display width
  syntaxStyle={syntaxStyle} // Syntax highlighting style
  cursorColor="#00FF00"    // Cursor color
  selectable              // Allow text selection
/>
```

### Keyboard Shortcuts

Default keybindings:
- `Cmd/Super+A` - Select all text

### Syntax Highlighting

To add syntax highlighting, wrap the textarea in a `<line-number>` container and use a `SyntaxStyle`:

```tsx
import { SyntaxStyle, RGBA } from "@opentui/core"

const syntaxStyle = SyntaxStyle.fromStyles({
  keyword: { fg: RGBA.fromHex("#ff6b6b") },
  string: { fg: RGBA.fromHex("#51cf66") },
  default: { fg: RGBA.fromHex("#ffffff") },
})

<textarea
  initialValue={code}
  syntaxStyle={syntaxStyle}
  onContentChange={(event) => handleChange(event)}
  focused
/>
```

## Select Component

List selection for choosing from options.

### Basic Usage

```tsx
// React
<select
  options={[
    { name: "Option 1", description: "First option", value: "1" },
    { name: "Option 2", description: "Second option", value: "2" },
    { name: "Option 3", description: "Third option", value: "3" },
  ]}
  onSelect={(index, option) => {
    console.log("Selected:", option.name)  // Called when Enter is pressed
  }}
  focused
/>

// Core
const select = new SelectRenderable(renderer, {
  id: "menu",
  options: [
    { name: "Option 1", description: "First option", value: "1" },
    { name: "Option 2", description: "Second option", value: "2" },
  ],
})
select.on(SelectRenderableEvents.ITEM_SELECTED, (index, option) => {
  console.log("Selected:", option.name)  // Called when Enter is pressed
})
select.focus()
```

### Option Format

```typescript
interface SelectOption {
  name: string          // Display text
  description?: string  // Optional description shown below
  value?: any          // Associated value
}
```

### Styling

```tsx
<select
  height={8}                    // Visible height
  selectedIndex={0}             // Initially selected
  showScrollIndicator           // Show scroll arrows
  selectedBackgroundColor="#333"
  selectedTextColor="#fff"
  highlightBackgroundColor="#444"
/>
```

### Navigation

Default keybindings:
- `Up` / `k` - Move up
- `Down` / `j` - Move down
- `Enter` - Select item

### Events

**Important**: `onSelect` and `onChange` serve different purposes:

| Event | Trigger | Use Case |
|-------|---------|----------|
| `onSelect` | **Enter key pressed** - user confirms selection | Perform action with selected item |
| `onChange` | **Arrow keys** - user navigates list | Preview, update UI as user browses |

```tsx
// React
<select
  onSelect={(index, option) => {
    // Called when Enter is pressed - selection confirmed
    console.log("User selected:", option.name)
    performAction(option)
  }}
  onChange={(index, option) => {
    // Called when navigating with arrow keys
    console.log("Browsing:", option.name)
    showPreview(option)
  }}
/>

// Core
select.on(SelectRenderableEvents.ITEM_SELECTED, (index, option) => {
  // Called when Enter is pressed
})
select.on(SelectRenderableEvents.SELECTION_CHANGED, (index, option) => {
  // Called when navigating with arrow keys
})
```

## Tab Select Component

Horizontal tab-based selection.

### Basic Usage

```tsx
// React
<tab-select
  options={[
    { name: "Home", description: "Dashboard view" },
    { name: "Settings", description: "Configuration" },
    { name: "Help", description: "Documentation" },
  ]}
  onSelect={(index, option) => {
    console.log("Tab selected:", option.name)  // Called when Enter is pressed
  }}
  focused
/>

// Core
const tabs = new TabSelectRenderable(renderer, {
  id: "tabs",
  options: [...],
  tabWidth: 20,
})
tabs.on(TabSelectRenderableEvents.ITEM_SELECTED, (index, option) => {
  console.log("Tab selected:", option.name)  // Called when Enter is pressed
})
tabs.focus()
```

### Events

Same pattern as Select - `onSelect` for Enter key, `onChange` for navigation:

```tsx
<tab-select
  onSelect={(index, option) => {
    // Called when Enter is pressed - switch to tab
    setActiveTab(index)
  }}
  onChange={(index, option) => {
    // Called when navigating with arrow keys
    showTabPreview(option)
  }}
/>
```

### Styling

```tsx
// React
<tab-select
  tabWidth={20}                // Width of each tab
  selectedIndex={0}            // Initially selected tab
/>

```

### Navigation

Default keybindings:
- `Left` / `[` - Previous tab
- `Right` / `]` - Next tab
- `Enter` - Select tab

## Focus Management

### Single Focused Input

```tsx
function SingleInput() {
  return <input placeholder="I'm focused" focused />
}
```

### Multiple Inputs with Focus State

```tsx
// React
function Form() {
  const [focusIndex, setFocusIndex] = useState(0)
  const fields = ["name", "email", "message"]
  
  useKeyboard((key) => {
    if (key.name === "tab") {
      setFocusIndex(i => (i + 1) % fields.length)
    }
  })
  
  return (
    <box flexDirection="column" gap={1}>
      {fields.map((field, i) => (
        <input
          key={field}
          placeholder={`Enter ${field}`}
          focused={i === focusIndex}
        />
      ))}
    </box>
  )
}
```

### Focus Methods (Core)

```typescript
input.focus()      // Give focus
input.blur()       // Remove focus
input.isFocused()  // Check focus state
```

## Form Patterns

### Login Form

```tsx
function LoginForm() {
  const [username, setUsername] = useState("")
  const [password, setPassword] = useState("")
  const [focusField, setFocusField] = useState<"username" | "password">("username")
  
  useKeyboard((key) => {
    if (key.name === "tab") {
      setFocusField(f => f === "username" ? "password" : "username")
    }
    if (key.name === "enter") {
      handleLogin()
    }
  })
  
  return (
    <box flexDirection="column" gap={1} border padding={2}>
      <box flexDirection="row" gap={1}>
        <text>Username:</text>
        <input
          value={username}
          onInput={setUsername}
          focused={focusField === "username"}
          width={20}
        />
      </box>
      <box flexDirection="row" gap={1}>
        <text>Password:</text>
        <input
          value={password}
          onInput={setPassword}
          focused={focusField === "password"}
          width={20}
        />
      </box>
    </box>
  )
}
```

### Search with Results

```tsx
function SearchableList({ items, onItemSelected }) {
  const [query, setQuery] = useState("")
  const [focusSearch, setFocusSearch] = useState(true)
  const [preview, setPreview] = useState(null)
  
  const filtered = items.filter(item =>
    item.toLowerCase().includes(query.toLowerCase())
  )
  
  useKeyboard((key) => {
    if (key.name === "tab") {
      setFocusSearch(f => !f)
    }
  })
  
  return (
    <box flexDirection="column">
      <input
        value={query}
        onInput={setQuery}
        placeholder="Search..."
        focused={focusSearch}
      />
      <select
        options={filtered.map(item => ({ name: item }))}
        focused={!focusSearch}
        height={10}
        onSelect={(index, option) => {
          // Enter pressed - confirm selection
          onItemSelected(option)
        }}
        onChange={(index, option) => {
          // Navigating - show preview
          setPreview(option)
        }}
      />
    </box>
  )
}
```

## Gotchas

### Focus Required

Inputs must be focused to receive keyboard input:

```tsx
// WRONG - won't receive input
<input placeholder="Type here" />

// CORRECT
<input placeholder="Type here" focused />
```

### Select Options Format

Options must be objects with `name` property:

```tsx
// WRONG
<select options={["a", "b", "c"]} />

// CORRECT
<select options={[
  { name: "A", description: "Option A" },
  { name: "B", description: "Option B" },
]} />
```

