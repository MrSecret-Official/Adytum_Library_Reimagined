# Adytum Reimagined Detailed Documentation

This guide provides a comprehensive technical reference for the Adytum Reimagined UI library, covering execution, automatic folder structures, window initialization, layout components, interactive input elements, and state persistence systems.

---

## Getting Started

### Basic Initialization
Load and run the library within your executor environment using the loader URL:

```lua
local Ady_Lib = loadstring(game:HttpGet('https://raw.githubusercontent.com/MrSecret-Official/Adytum_Library_Reimagined_Before_Release_Test/refs/heads/main/Loader.lua'))()
```

### Automatic File Operations
When a window is created, the library automatically builds a structured directory hierarchy inside your executor's `workspace` folder:

1. **Root Directory**: `Adytum_libraryfolder` (created on first run).
2. **Hub Directory**: `Adytum_libraryfolder/Ady...{Title}_by...{DevName}`. Isolate configurations and assets for your specific project hub.
3. **Asset Directory**: `Adytum_libraryfolder/Ady...{Title}_by...{DevName}/Assets`. Automatically saves assets downloaded from external URLs for local rendering.
4. **Game Directory**: `Adytum_libraryfolder/Ady...{Title}_by...{DevName}/Ady...{GameName}_ID{PlaceId}`. Isolates settings per game.
5. **Config Directory**: `Adytum_libraryfolder/Ady...{Title}_by...{DevName}/Ady...{GameName}_ID{PlaceId}/Configs`. Houses all serialized JSON config files.

### Multi-Instance Safety
To prevent duplicate windows and memory leaks when restarting scripts during development, the library includes built-in multi-instance safety. If a script with the same `DevName` and `Title` is executed again in the same game, the library automatically unloads the previous GUI session (disconnecting connections, closing threads, and cleaning up UI elements) before loading the new window.

### Global GUI Operations

#### `Library:Unload()`
Completely unloads the library GUI. This removes the interface, disconnects all internal event connections (such as input handlers), stops running threads, and restores the default mouse icon behavior.
```lua
Library:Unload()
```

#### `Library:Notification(title, description, duration)`
Triggers a slide-in alert notification box at the bottom-right of the screen.
```lua
Library:Notification("Alert", "Something happened!", 3)
```
- `title` (string): Header text of the notification banner.
- `description` (string): Informative body text describing the event.
- `duration` (number): Lifespan of the notification in seconds before it fades out.

---

## Window Initialization

### `Library:Window(configuration)`
Creates and launches the main graphical interface frame. The **Settings** tab (handling theme editing, configuration file loading, UI keybind, and server helper buttons) is generated automatically and attached as the last sidebar tab.

```lua
local Window = Ady_Lib:Window({
    Title = "Blox Hub",
    DevName = "MrSecret",
    Logo = "77218680285262",
    FadeTime = 0.4,
    TitlePosition = "Topbar",
    AllowConfigExport = true,
    Credits = {
        { Name = "MrSecret", Role = "Lead Developer" }
    },
    MinSize = Vector2.new(500, 350),
    MaxSize = Vector2.new(1000, 700)
})
```

#### Parameters:
- `Title` (string): The title text of your hub.
- `DevName` (string): Developer's name, used to construct folder paths.
- `Logo` (string): Asset ID for the image rendering on the top of the side panel.
- `FadeTime` (number): Duration of opening, closing, and tab switching animations.
- `TitlePosition` (string): Sets where the title renders by default:
  - `"Topbar"`: Left-aligned in the top titlebar.
  - `"Logo"`: Centered in the sidebar below the logo.
  - `"None"`: Hidden.
- `AllowConfigExport` (boolean): Hides or shows the configuration copy/paste box controls in the Settings tab.
- `Credits` (table): List of credits entries formatted as `{ { Name = "name", Role = "role" } }`.
- `MinSize` (Vector2, optional): Restricts the minimum window size when resized by users.
- `MaxSize` (Vector2, optional): Restricts the maximum window size when resized by users.

---

## Core Systems

### Topbar & Minimization
The window includes a 30-pixel top header bar:
- **Secondary Drag Handle**: Allows dragging and moving the window frame across the screen.
- **Minimization Button `[—]`**: Collapses the interface to only show the topbar. Side panel, page content, and resize handles are hidden and disabled in this state. Clicking the maximize icon `[□]` restores the window to its full size.

### Autoload System
On initialization, the library checks the game's configs directory for files starting with `[AT] ` (e.g., `[AT] default.json`). If found, it automatically applies the saved flag configurations and color scheme.

### Clipboard Export & Import Modals
Invoking `Library:OpenConfigBox(title, mode, presetText, callback)` overlays a central textbox modal:
- **Export Mode**: Displays the JSON configuration string in a read-only multi-line textbox, offering a Copy button (supporting `setclipboard`).
- **Import Mode**: Provides an empty input field for pasting JSON payloads and triggers the callback parameter upon clicking Import.

### HUD Overlay Widgets (Watermark & Keybind List)
Two auxiliary HUD overlay panels are initialized automatically when a window is created and are accessible via the returned window object:

#### `Window.Watermark`
A draggable on-screen display panel that shows real-time stats including the hub's name, active frame rate (FPS), and network latency (Ping).
- **Set Visibility**:
  ```lua
  Window.Watermark:SetVisibility(true) -- Shows the watermark
  Window.Watermark:SetVisibility(false) -- Hides the watermark
  ```

#### `Window.KeybindList`
A draggable on-screen overlay list displaying the currently active keybind components, their trigger keys, and activation modes (e.g., "Toggle", "Hold").
- **Set Visibility**:
  ```lua
  Window.KeybindList:SetVisibility(true) -- Shows the keybind list
  Window.KeybindList:SetVisibility(false) -- Hides the keybind list
  ```

---

## GUI Customization

### Corner Radius Adjustment
The interface supports custom corner roundedness values for various components. You can adjust these values dynamically.
```lua
Library:SetCornerRadius(type, value)
```
- `type` (string): The category of element corners to adjust:
  - `"Window"`: Main GUI window background container corners (default: 6px).
  - `"Boxes"`: Section content boxes and background containers (default: 3px).
  - `"Sliders"`: Slider track indicator boxes (default: 0px).
- `value` (number): Corner radius size in pixels (clamped between the category's default minimum and 32 pixels).

---

## Layout Components

### Pages & SubPages

#### `Window:Page(data)`
Inserts a main tab button in the sidebar layout.
```lua
local MainPage = Window:Page({
    Name = "Main",
    SubPages = false
})
```
- `Name` (string): Label string on the tab button.
- `SubPages` (boolean): Set to `true` if you plan to nest subpages inside this tab.

#### `Page:SubPage(data)`
Nests a sub-tab under a parent page that has `SubPages = true`.
```lua
local SubPage = MainPage:SubPage({
    Name = "Combat",
    Columns = 2
})
```
- `Columns` (number): Defines whether the container uses a `1` or `2` column layout structure.

---

### Sections

#### `Page:Section(data)` / `SubPage:Section(data)`
Creates a container box inside a page or subpage.
```lua
local CombatSection = SubPage:Section({
    Name = "Aura Settings",
    Side = 1
})
```
- `Name` (string): Heading header text of the section.
- `Side` (number): Column placement (`1` for left column, `2` for right column).

---

## Input Elements

#### `Section:Toggle(data)`
Creates an interactive binary switch.
```lua
local KillAura = CombatSection:Toggle({
    Name = "Kill Aura",
    Flag = "AuraToggle",
    Default = false,
    Callback = function(State)
        print("Kill Aura active state:", State)
    end
})
```

##### Inline Toggle Sub-Elements:
- **`Toggle:Colorpicker(data)`**: Inserts a colorpicker option next to the toggle label.
  ```lua
  KillAura:Colorpicker({
      Flag = "AuraColor",
      Default = Color3.fromRGB(255, 0, 0),
      Alpha = 0,
      Callback = function(Color)
          print("Aura Color set to:", Color)
      end
  })
  ```
- **`Toggle:Keybind(data)`**: Attaches a keybind controller to trigger the toggle.
  ```lua
  KillAura:Keybind({
      Flag = "AuraKeybind",
      Default = Enum.KeyCode.V,
      Mode = "Toggle", -- "Toggle" or "Hold"
      Callback = function(State)
          print("Keybind triggered. Current state:", State)
      end
  })
  ```

#### `Section:Slider(data)`
A draggable horizontal slider bar for controlling numeric ranges.
```lua
CombatSection:Slider({
    Name = "Attack Range",
    Flag = "AttackRange",
    Min = 1,
    Max = 25,
    Default = 5,
    Decimals = 0.5,
    Suffix = " studs",
    Callback = function(Value)
        print("Range set to:", Value)
    end
})
```

#### `Section:Dropdown(data)`
Presents a selection list.
```lua
CombatSection:Dropdown({
    Name = "Target Priority",
    Flag = "TargetList",
    Items = { "Players", "NPCs", "Bosses" },
    Default = "NPCs",
    Multi = false,
    Callback = function(Selection)
        print("Target prioritised:", Selection)
    end
})
```

#### `Section:Textbox(data)`
An input field for entering text strings.
```lua
CombatSection:Textbox({
    Name = "Name Filter",
    Flag = "NameFilter",
    Default = "",
    Placeholder = "Search username...",
    Numeric = false,
    Finished = true, -- If true, triggers callback only when focus is lost or Enter is pressed.
    Callback = function(Text)
        print("Filter string:", Text)
    end
})
```

#### `Section:Searchbox(data)`
A listbox selection menu equipped with an integrated search text field at the top. It allows users to filter list items dynamically by typing.
```lua
local SearchMenu = CombatSection:Searchbox({
    Name = "Target Selection",
    Flag = "SearchMenu",
    Items = { "Alice", "Bob", "Charlie", "David" },
    Default = "Alice",
    Multi = false,
    Callback = function(Selection)
        print("Selected item:", Selection)
    end
})
```
##### Parameters:
- `Name` (string): Title text shown at the top of the search widget.
- `Flag` (string): Flag identifier for config persistence.
- `Items` (table): Table of string options.
- `Default` (string/table): Default option value (or table of values if `Multi` is enabled).
- `Multi` (boolean): Allows selecting multiple items simultaneously.
- `Callback` (function): Triggered when selections change, returning the active value(s).

##### Methods on Returned Searchbox Object:
- **`:Get()`**: Returns the current value (a string, or table of strings if multi-select is enabled).
- **`:Set(value)`**: Programmatically sets the active selection (string or list of strings).
- **`:Add(option)`**: Dynamically appends a new string option to the selection list.
- **`:Remove(option)`**: Removes a string option from the selection list.
- **`:Refresh(list)`**: Replaces all existing list options with a new table of strings.
- **`:SetVisibility(bool)`**: Toggles whether the searchbox container is visible.

#### `Section:Label(text)`
Renders basic text information inside a section.
```lua
local InfoLabel = CombatSection:Label("Usage notes: Excessive ranges may cause disconnects.")
```
- **`Label:Keybind(data)`**: Binds a key keybind option to a label.
  ```lua
  InfoLabel:Keybind({
      Flag = "LabelKeybind",
      Default = Enum.KeyCode.F3,
      Mode = "Toggle",
      Callback = function(Value)
          print("Label keybind activated:", Value)
      end
  })
  ```
- **`Label:Colorpicker(data)`**: Attaches an inline colorpicker next to the label.
  ```lua
  InfoLabel:Colorpicker({
      Flag = "LabelColorpicker",
      Default = Color3.fromRGB(0, 255, 0),
      Alpha = 0,
      Callback = function(Color, Alpha)
          print("Label color chosen:", Color)
      end
  })
  ```

#### `Section:Button()`
Creates a button row supporting multiple button click action nodes.
```lua
local Row = CombatSection:Button()

Row:Add("Kill All", function()
    print("Action: Kill All triggered")
end)

Row:Add("Clear Target", function()
    print("Action: Clear Target triggered")
end)
```

---

## Custom Theme Keys Reference

When styling the UI, the theme map requires configuring these twelve exact keys:

| Theme Key | Type | Description |
|---|---|---|
| `Background` | Color3 | Main background color of the window frame |
| `Border` | Color3 | Inner border border outline color |
| `Inline` | Color3 | Background color for sections and container boxes |
| `Hovered Element` | Color3 | Highlight color when mouse is hovering interactive elements |
| `Page Background` | Color3 | Color of the content page areas |
| `Outline` | Color3 | Outlines of sections and input boxes |
| `Element` | Color3 | Default color of unselected toggles, textboxes, and sliders |
| `Gradient` | Color3 | Secondary color used in interactive gradients |
| `Text` | Color3 | Color of general label and button texts |
| `Text Stroke` | Color3 | Shadow/stroke offset color for texts |
| `Placeholder Text` | Color3 | Text color for placeholder and description labels |
| `Accent` | Color3 | Core active element fill color (e.g., active toggles, slider fills) |

### Theme Management & Preset Configurations
Developers can configure the theme preset system, limit layout options, and programmatically adjust color themes.

#### Global Control Flags
Modify these boolean parameters on the global `Library` object before initializing any windows to customize what options are available to the user in the Settings panel:
```lua
Library.AllowThemePresets = true   -- Set to false to hide preset theme dropdown menus
Library.AllowAdvancedTheming = true -- Set to false to hide advanced theme import/export / saving panels
Library.AllowConfigExport = true    -- Set to false to hide config import/export panels
```

#### Developer Preset Registration & Control
You can register and apply custom color scheme presets programmatically:
- **`Library:RegisterThemePreset(name, colorTable)`**: Registers a custom color preset. The `colorTable` should be a dictionary mapping the custom theme keys to `Color3` values.
- **`Library:SetThemePreset(name)`**: Instantly applies a registered preset theme by name.
```lua
Library:RegisterThemePreset("Forest", {
    ["Background"] = Color3.fromRGB(15, 25, 20),
    ["Border"] = Color3.fromRGB(30, 45, 35),
    ["Inline"] = Color3.fromRGB(20, 32, 25),
    ["Hovered Element"] = Color3.fromRGB(35, 55, 40),
    ["Page Background"] = Color3.fromRGB(20, 30, 25),
    ["Outline"] = Color3.fromRGB(45, 65, 50),
    ["Element"] = Color3.fromRGB(25, 40, 30),
    ["Gradient"] = Color3.fromRGB(35, 55, 40),
    ["Text"] = Color3.fromRGB(220, 240, 225),
    ["Text Stroke"] = Color3.fromRGB(10, 15, 12),
    ["Placeholder Text"] = Color3.fromRGB(130, 160, 140),
    ["Accent"] = Color3.fromRGB(46, 179, 102)
})

Library:SetThemePreset("Forest")
```

---

## State Persistence (Flags)

All toggle states, colorpicker selections, dropdown settings, and textbox inputs are mapped directly to `Library.Flags` using their defined string identifiers.

### Reading Flag Value
```lua
local AuraActive = Library.Flags["AuraToggle"]
```

### Manual Configuration Serialisation
To programmatically save or load configurations:
- `Library:GetConfig()`: Returns a JSON-formatted string of all currently set flags.
- `Library:LoadConfig(jsonString)`: Restores flag states and updates input UI positions.
- `Library:GetThemeConfig()`: Returns a JSON-formatted string of the active theme colors.
- `Library:LoadThemeConfig(jsonString)`: Applies the theme colors to the active interface.
