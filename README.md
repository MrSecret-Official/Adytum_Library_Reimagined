# Adytum Reimagined

Adytum Reimagined is a feature-rich, high-performance UI library designed for script developers looking to build clean, modular, and highly customizable interfaces. The library handles background operations such as theme synchronization, settings persistence, autoloading configs, and window drag/minimize operations automatically.

---

## Media

Reimagined GUI (Censored Information):

![Main UI Demonstration](https://github.com/MrSecret-Official/Adytum_Library_Reimagined/blob/main/Github_assets/Library.png)

---

## Key Features

### Topbar and Minimization
The library features a dedicated 30-pixel topbar that serves as a drag handle and houses the title and window control button. Clicking the minimize button collapses the UI into a slim horizontal topbar, automatically hiding components and disabling resizing. Clicking it again restores the UI to its full size.
![Topbar minimized](https://github.com/MrSecret-Official/Adytum_Library_Reimagined/blob/main/Github_assets/Minim_gui.png)

### Custom Folder Hierarchy
When creating a window, the library automatically organizes files to prevent clutter across different hubs and games:
- Directory: `Adytum_libraryfolder`
- Hub Directory: `Adytum_libraryfolder/Ady...{Title}_by...{DevName}`
- Game Config Directory: `Adytum_libraryfolder/Ady...{Title}_by...{DevName}/Ady...{GameName}_ID{PlaceId}/Configs`
- Hub Asset Directory: `Adytum_libraryfolder/Ady...{Title}_by...{DevName}/Assets`

### Theme Presets and Advanced Customization
Three theme presets are registered by default:
- Default: The library's ocean dark theme.
- Midnight: A deep violet theme.
- Ember: A warm orange/red theme.
- More...

Check Default Preset Themes here -> https://github.com/MrSecret-Official/Adytum_Library_Reimagined/tree/main/Github_assets/Themes

Users can switch presets via the Preset dropdown inside Settings -> Theming, and developers can register custom presets. The library also features options to completely toggle presets, advanced theming panels, and export/import tools.

### Clipboard Config Export and Import
Settings configs and themes can be exported or imported via a clipboard-based pop-up box. When exporting, the JSON payload is rendered inside a textbox with a Copy action (supporting `setclipboard`). When importing, a textbox is provided to paste the JSON, parsing and applying the config live.

### Autoload System
Any config file prefixed with `[AT]` (e.g., `[AT] default.json`) saved in the game's configs folder is automatically loaded on startup, restoring the user's preferred settings and theme.

### Interactive Searchbox Element
Easily filter through long option lists with a responsive search input element. The list updates dynamically as the user types, making option selection quick and intuitive.

### Customizable Corner Roundedness
Adjust the corner radius of the main GUI window, section boxes, and sliders dynamically to match your desired visual aesthetic.

### On-Screen HUD Widgets (Watermark & Keybind List)
Track active keybind states and display real-time FPS and Ping counters on-screen with custom movable overlay widgets that can be configured or toggled by the user.

### Inline Interactive Labels
Attach colorpickers or keybinds directly to information labels to build compact, efficient GUI layouts.

### Global Toast Notifications
Display styled slide-in alert notifications with custom titles, descriptions, and durations.

### Multi-Instance Safety
Automatically replaces and cleans up any existing GUI instance running with the same DevName and Title when the script is re-executed, preventing duplicate windows and saving system memory.

### UI & Font Scaling
Adjust the visual size of the interface (85% to 120%) and general library text sizes (85% to 125%) dynamically from the settings panel to ensure comfortable reading.

### Reduce Motion Mode
Instantly disables or minimizes all fade/slide transitions and animations for users who prefer faster rendering or have performance-limited execution environments.

### High Contrast Mode
Automatically boosts the current theme colors mathematically to ensure accessibility, locking editing of key color roles to prevent unreadable configurations.

### Draggable Inventory Viewer HUD
Provides a built-in HUD overlay to view a target player's thumbnail, health, distance, and tools in a visual grid, controlled dynamically via scripts.

### Settings Persistence
Automatically saves and restores user preferences (HUD visibility, window scale, font scale, and animation/motion settings) between execution sessions.

---

## Documentation

For a complete breakdown of all GUI parameters, layout configurations, custom elements, and helper methods, please refer directly to the [Detailed Documentation](DOCUMENTATION.md).

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
    DevName = "Owner",
    Logo = "77218680285262",
    FadeTime = 0.4,
    TitlePosition = "Topbar",
    AllowConfigExport = true,
    Credits = {
        { Name = "Owner", Role = "Developer" }
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

---

## Detailed Documentation

For a complete breakdown of all GUI parameters, design structures, custom layout configurations, and helper methods, refer to the [Detailed Documentation](DOCUMENTATION.md).
