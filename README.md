# Adytum Reimagined

Adytum Reimagined is a feature-rich, high-performance UI library designed for script developers looking to build clean, modular, and highly customizable interfaces. The library handles background operations such as theme synchronization, settings persistence, autoloading configs, and window drag/minimize operations automatically.

---

## Media and Demonstration

Insert your demonstration images or videos below:

![Main UI Demonstration](https://raw.githubusercontent.com/MrSecret-Official/Adytum_Library_Reimagined_Before_Release_Test/refs/heads/main/assets/preview.png)

```
[Insert Video Demo Link or Embed Here]
```

---

## Key Features

### Topbar and Minimization
The library features a dedicated 30-pixel topbar that serves as a drag handle and houses the title and window control button. Clicking the minimize button collapses the UI into a slim horizontal topbar, automatically hiding components and disabling resizing. Clicking it again restores the UI to its full size.

### Custom Folder Hierarchy
When creating a window, the library automatically organizes files to prevent clutter across different hubs and games:
- Directory: `Adytum_libraryfolder`
- Hub Directory: `Adytum_libraryfolder/Ady...{Title}_by...{DevName}`
- Game Config Directory: `Adytum_libraryfolder/Ady...{Title}_by...{DevName}/Ady...{GameName}_ID{PlaceId}/Configs`
- Hub Asset Directory: `Adytum_libraryfolder/Ady...{Title}_by...{DevName}/Assets`

### Theme Presets
Three theme presets are registered by default:
- Default: The library's ocean dark theme.
- Midnight: A deep violet theme.
- Ember: A warm orange/red theme.
Users can switch presets via the Preset dropdown inside Settings -> Theming, and developers can register custom presets.

### Clipboard Config Export and Import
Settings configs and themes can be exported or imported via a clipboard-based pop-up box. When exporting, the JSON payload is rendered inside a textbox with a Copy action (supporting `setclipboard`). When importing, a textbox is provided to paste the JSON, parsing and applying the config live.

### Autoload System
Any config file prefixed with `[AT]` (e.g., `[AT] default.json`) saved in the game's configs folder is automatically loaded on startup, restoring the user's preferred settings and theme.

---

## Developer API Reference

The library file is located at [Library_Reimagined.lua](file:///c:/Users/rubem/Documents/Projects/Adytum_Reimagined/Library_Reimagined.lua).

### Library Initialization

```lua
local Ady_Lib = loadstring(game:HttpGet('https://raw.githubusercontent.com/MrSecret-Official/Adytum_Library_Reimagined_Before_Release_Test/refs/heads/main/Loader.lua'))()
```

### Library methods

#### `Library:Window(data)`
Creates the main user interface window.
- `data.Logo` (string): The asset ID for the side-panel logo.
- `data.Title` (string): The title text of the window.
- `data.DevName` (string): The developer's name, used to build the folder structure.
- `data.TitlePosition` (string): Initial position of the title: `"Topbar"`, `"Logo"`, or `"None"`.
- `data.FadeTime` (number): Fade animation speed in seconds.
- `data.MinSize` (Vector2, optional): Minimum size boundaries for resizing.
- `data.MaxSize` (Vector2, optional): Maximum size boundaries for resizing.
- `data.AllowConfigExport` (boolean, optional): Hides or shows the configuration copy/paste box options.
- `data.Credits` (table, optional): A list of credits entries formatted as `{ { Name = "name", Role = "role" } }`.

#### `Library:RegisterThemePreset(name, colorTable)`
Registers a custom theme preset that users can select in settings.
- `name` (string): Unique identifier for the preset.
- `colorTable` (table): 12-key theme mapping. Refer to the table below for keys.

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

#### `Library:SetThemePreset(name)`
Applies a registered theme preset by its name.

#### `Library:OpenConfigBox(title, mode, presetText, callback)`
Opens the modal overlay for copying or pasting configs.
- `title` (string): Title of the popup window.
- `mode` (string): `"Export"` (read-only textbox + Copy button) or `"Import"` (editable textbox + Import button).
- `presetText` (string): Pre-filled text inside the textbox (for Export).
- `callback` (function): Triggers when clicking Import, passing the pasted string.

---

## Component API Reference

### Page and SubPages

#### `Window:Page(data)`
Creates a new sidebar tab.
- `data.Name` (string): Name of the page tab.
- `data.SubPages` (boolean, optional): Set to `true` if you plan to nest subpages inside this page.

#### `Page:SubPage(data)`
Creates a subpage container nested inside a page.
- `data.Name` (string): Name of the subpage tab.
- `data.Columns` (number): Number of vertical layout columns (1 or 2).

### Sections

#### `Page:Section(data)` / `SubPage:Section(data)`
Creates a container box inside a page or subpage.
- `data.Name` (string): Header title of the section.
- `data.Side` (number): 1 (left column) or 2 (right column).

### Input Elements

#### `Section:Toggle(data)`
Creates a binary switch.
- `data.Name` (string): Label of the toggle.
- `data.Flag` (string): Config flag key.
- `data.Default` (boolean): Default checked state.
- `data.Callback` (function): Returns `true` or `false` on change.

#### `Toggle:Colorpicker(data)`
Attaches a colorpicker to a toggle.
- `data.Flag` (string): Config flag key.
- `data.Default` (Color3): Default color.
- `data.Alpha` (number): Default transparency level (0 to 1).
- `data.Callback` (function): Returns `Color3` color.

#### `Toggle:Keybind(data)`
Attaches a keybind controller to a toggle.
- `data.Flag` (string): Config flag key.
- `data.Default` (Enum.KeyCode): Default key.
- `data.Mode` (string): `"Toggle"` or `"Hold"`.
- `data.Callback` (function): Returns key activation state.

#### `Section:Slider(data)`
Creates a numeric draggable slider bar.
- `data.Name` (string): Slider label.
- `data.Flag` (string): Config flag key.
- `data.Min` (number): Minimum value.
- `data.Max` (number): Maximum value.
- `data.Default` (number): Default value.
- `data.Decimals` (number): Decimal precision (e.g., 1, 0.1, 0.01).
- `data.Suffix` (string): Label suffix (e.g., `"%"`).
- `data.Callback` (function): Returns selected value.

#### `Section:Dropdown(data)`
Creates a list selection dropdown menu.
- `data.Name` (string): Dropdown label.
- `data.Flag` (string): Config flag key.
- `data.Items` (table): Array of selectable string values.
- `data.Default` (string): Default selected item.
- `data.Multi` (boolean): Enables multi-item selection.
- `data.Callback` (function): Returns selected string.

#### `Section:Textbox(data)`
Creates a text input field.
- `data.Name` (string): Textbox label.
- `data.Flag` (string): Config flag key.
- `data.Default` (string): Default text.
- `data.Placeholder` (string): Background text when empty.
- `data.Numeric` (boolean): Restricts input to numbers.
- `data.Finished` (boolean): Triggers callback only when focus is lost or Enter is pressed.
- `data.Callback` (function): Returns typed string.

---

## Example Test Script

Use the script below to run a verification environment showcasing all supported elements, callback signals, and Settings components.

```lua
--[[
    Test_Library_Reimagined.lua
    Demo / test script for Adytum_Library_Reimagined

    Loads the library from your GitHub repo and builds a window
    with one page/section per component type so you can sanity-check
    that everything renders and fires callbacks correctly.
]]

local Ady_Lib = loadstring(game:HttpGet('https://raw.githubusercontent.com/MrSecret-Official/Adytum_Library_Reimagined_Before_Release_Test/refs/heads/main/Loader.lua'))()

-- === Window ===
-- Watermark, KeybindList and the Settings tab (Theming, Configs, Autoload,
-- UI Keybind, Panic/Rejoin/Serverhop) are created automatically and are
-- NOT configurable from here. The Settings tab is guaranteed to always be
-- the LAST tab, regardless of how many pages you add below (Home, Test, etc.).
--
-- MinSize / MaxSize (Vector2) are optional: if you don't pass them, the
-- window defaults to a minimum equal to its initial Size and an effectively
-- unbounded maximum, same as before. Uncomment below to set custom limits.
local Window = Ady_Lib:Window({
    Title = "Blox Hub",
    DevName = "rubem",
    Logo = "77218680285262",
    FadeTime = 0.4,
    TitlePosition = "Topbar",
    AllowConfigExport = true,
    Credits = {
        { Name = "rubem", Role = "Developer" }
    }
    -- MinSize = Vector2.new(500, 350),
    -- MaxSize = Vector2.new(1000, 700),
})

-- === Main Page ===
local MainPage = Window:Page({ Name = "Main", Columns = 2 })

local ToggleSection = MainPage:Section({ Name = "Toggles", Side = 1 })

ToggleSection:Toggle({
    Name = "Basic Toggle",
    Flag = "TestToggle",
    Default = false,
    Callback = function(Value)
        print("[Test] Basic Toggle ->", Value)
    end
})

local ColorToggle = ToggleSection:Toggle({
    Name = "Toggle w/ Colorpicker",
    Flag = "TestToggleColor",
    Default = false,
    Callback = function(Value)
        print("[Test] Toggle w/ Colorpicker ->", Value)
    end
})

ColorToggle:Colorpicker({
    Flag = "TestToggleColorpicker",
    Default = Color3.fromRGB(58, 138, 224),
    Alpha = 0,
    Callback = function(Color)
        print("[Test] Colorpicker ->", Color)
    end
})

local KeybindToggle = ToggleSection:Toggle({
    Name = "Toggle w/ Keybind",
    Flag = "TestToggleKeybind",
    Default = false,
    Callback = function(Value)
        print("[Test] Toggle w/ Keybind ->", Value)
    end
})

KeybindToggle:Keybind({
    Flag = "TestKeybind",
    Default = Enum.KeyCode.RightShift,
    Mode = "Toggle",
    Callback = function(Value)
        print("[Test] Keybind fired ->", Value)
    end
})

local ButtonSection = MainPage:Section({ Name = "Buttons", Side = 1 })

ButtonSection:Button():Add("Click Me", function()
    print("[Test] Button clicked")
    Ady_Lib:Notification("Test", "Button was clicked!", 3)
end)

local InputSection = MainPage:Section({ Name = "Inputs", Side = 2 })

InputSection:Slider({
    Name = "Test Slider",
    Flag = "TestSlider",
    Min = 0,
    Max = 100,
    Default = 50,
    Decimals = 0,
    Suffix = "%",
    Callback = function(Value)
        print("[Test] Slider ->", Value)
    end
})

InputSection:Dropdown({
    Name = "Test Dropdown",
    Flag = "TestDropdown",
    Items = { "Option 1", "Option 2", "Option 3" },
    Default = "Option 1",
    Multi = false,
    Callback = function(Value)
        print("[Test] Dropdown ->", Value)
    end
})

InputSection:Textbox({
    Name = "Test Textbox",
    Flag = "TestTextbox",
    Default = "",
    Placeholder = "Type something...",
    Numeric = false,
    Finished = true,
    Callback = function(Value)
        print("[Test] Textbox ->", Value)
    end
})

local LabelSection = MainPage:Section({ Name = "Labels", Side = 2 })

LabelSection:Label("This is a plain label")

local KeybindLabel = LabelSection:Label("Label w/ Keybind")
KeybindLabel:Keybind({
    Flag = "TestLabelKeybind",
    Default = Enum.KeyCode.RightControl,
    Mode = "Toggle",
    Callback = function(Value)
        print("[Test] Label Keybind ->", Value)
    end
})

-- === SubPage test ===
local SubPage = MainPage:SubPage({ Name = "Nested SubPage", Columns = 1 })
local SubSection = SubPage:Section({ Name = "SubPage Section", Side = 1 })

SubSection:Toggle({
    Name = "SubPage Toggle",
    Flag = "TestSubPageToggle",
    Default = false,
    Callback = function(Value)
        print("[Test] SubPage Toggle ->", Value)
    end
})

Ady_Lib:Notification("Loaded", "Test script finished loading.", 5)
```
