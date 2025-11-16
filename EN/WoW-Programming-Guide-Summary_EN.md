# WoW Programming Practical Guide - Book Summary

**Source**: World of Warcraft Programming: A Guide and Reference for Creating WoW Addons (2nd Edition)
**Authors**: James Whitehead II (Cladhaire) & Rick Roe (Gazmik Fizzwidget)
**Pages**: 62,000+ lines of technical content

---

**⚠️ IMPORTANT - Lua 5.0 (WoW Vanilla 1.12)**

This guide is adapted for **WoW Vanilla 1.12** which uses **Lua 5.0**, not Lua 5.1+.

Critical differences:
- ❌ No `#` operator → Use `table.getn()` or `getn()`
- ❌ No `select()` → Use `arg[n]` and `arg.n`
- ❌ No `{...}` table literal → Use `arg` table directly
- ✅ Varargs via automatic `arg` table
- ✅ `arg1`, `arg2`, etc. in WoW events
- ✅ `this` in handlers (not `self`)

See [LUA-5.0-COMPATIBILITY_EN.md](LUA-5.0-COMPATIBILITY_EN.md) for complete details.

---

## Table of Contents

1. [Book Structure](#book-structure)
2. [Part I: Learning to Program](#part-i-learning-to-program)
3. [Part II: Programming in WoW](#part-ii-programming-in-wow)
4. [Part III: Advanced Techniques](#part-iii-advanced-techniques)
5. [Part IV: Reference](#part-iv-reference)
6. [Key Code Examples](#key-code-examples)
7. [Resources and Tools](#resources-and-tools)

---

## Book Structure

### General Organization

The book is divided into 5 major parts:

- **Part I**: Learning Lua and XML (Chapters 1-7)
- **Part II**: Programming in WoW (Chapters 8-14)
- **Part III**: Advanced Techniques (Chapters 15-26)
- **Part IV**: Reference (Chapters 27-30)
- **Part V**: Appendices (A-D)

### Target Audience

1. **Beginner programmers**: Start with Part I
2. **Intermediate developers**: Skim Part I, focus on Part II
3. **Experienced programmers**: Dive directly into Part III

---

## Part I: Learning to Program

### Chapter 1: Programming for WoW

#### What is an Addon?

An addon is a set of files that extend WoW's user interface:
- `.lua` files (logic/code)
- `.xml` files (graphical interface)
- `.toc` file (metadata)
- Media (textures, sounds)

#### Creating Your First Addon: HeyThere

```
HeyThere/
├── HeyThere.toc
└── HeyThere.lua
```

**HeyThere.toc**:
```toc
## Interface: 11200
## Title: Hey There
## Notes: My first addon
## Author: YourName

HeyThere.lua
```

**HeyThere.lua**:
```lua
print("Hey there, World of Warcraft!")
```

### Chapter 2: Lua Basics

#### Data Types

```lua
-- Numbers
local x = 42
local y = 3.14
local z = 1.5e10  -- Scientific notation
local hex = 0xFF  -- Hexadecimal (255)

-- Strings
local str1 = "Hello"
local str2 = 'World'
local str3 = [[Multi-line
string]]

-- Booleans
local isTrue = true
local isFalse = false

-- Nil
local nothing = nil

-- Type of a value
print(type(x))        -- "number"
print(type(str1))     -- "string"
print(type(isTrue))   -- "boolean"
print(type(nothing))  -- "nil"
```

#### String Operations

```lua
-- Concatenation
local full = "Hello" .. " " .. "World"  -- "Hello World"

-- Length
print(#"Hello")           -- 5
print(string.len("Hi"))   -- 2

-- Conversion
local num = tonumber("42")     -- 42
local str = tostring(42)       -- "42"

-- Comparison
print("abc" < "def")      -- true
print("Apple" == "apple") -- false (case sensitive)
```

#### Logical Operators

```lua
-- AND: returns first false/nil OR last value
print(true and "yes")     -- "yes"
print(false and "yes")    -- false
print(nil and "yes")      -- nil

-- OR: returns first true OR last value
print(true or "no")       -- true
print(false or "yes")     -- "yes"
print(nil or "default")   -- "default"

-- NOT: negation
print(not true)           -- false
print(not nil)            -- true
print(not 0)              -- false (0 is true in Lua!)
```

### Chapter 3: Functions and Control Structures

#### Defining Functions

```lua
-- Simple function
function sayHello()
    print("Hello!")
end

-- Function with arguments
function greet(name)
    print("Hello, " .. name)
end

-- Function with return
function add(a, b)
    return a + b
end

-- Local functions (recommended)
local function multiply(a, b)
    return a * b
end

-- Anonymous function
local square = function(x)
    return x * x
end
```

#### Conditional Structures

```lua
-- Simple if
if condition then
    -- code
end

-- If-else
if health < 20 then
    print("Low health!")
else
    print("Healthy")
end

-- If-elseif-else
if health < 20 then
    print("Critical!")
elseif health < 50 then
    print("Low")
elseif health < 80 then
    print("Medium")
else
    print("Full")
end

-- Simulated ternary operator
local status = (health > 50) and "Healthy" or "Wounded"
```

#### Loops

```lua
-- While
local i = 1
while i <= 10 do
    print(i)
    i = i + 1
end

-- Repeat-until (do-while equivalent)
local i = 1
repeat
    print(i)
    i = i + 1
until i > 10

-- Numeric for
for i = 1, 10 do
    print(i)  -- 1, 2, 3, ..., 10
end

for i = 10, 1, -1 do
    print(i)  -- 10, 9, 8, ..., 1
end

for i = 0, 100, 10 do
    print(i)  -- 0, 10, 20, ..., 100
end
```

### Chapter 4: Tables

#### Creation and Indexing

```lua
-- Empty table
local t = {}

-- Adding elements
t["key"] = "value"
t.name = "John"        -- Shortcut for t["name"]
t[1] = "first"
t[2] = "second"

-- Access
print(t["key"])        -- "value"
print(t.name)          -- "John"
print(t[1])            -- "first"

-- Initialized table
local player = {
    name = "Arthas",
    level = 80,
    class = "Death Knight"
}

-- Array
local colors = {"red", "green", "blue"}
print(colors[1])       -- "red" (indices start at 1!)
```

#### Table Functions

```lua
local t = {"alpha", "beta", "gamma"}

-- Length (Lua 5.0)
print(table.getn(t))   -- 3
print(getn(t))         -- 3 (global version)

-- Insert
table.insert(t, "delta")        -- Add to end
table.insert(t, 2, "zeta")      -- Insert at position 2

-- Remove
local last = table.remove(t)    -- Remove last
local second = table.remove(t, 2) -- Remove position 2

-- Concatenate
print(table.concat(t, ", "))    -- "alpha, beta, gamma"

-- Sort
table.sort(t)
print(table.concat(t, ", "))    -- "alpha, beta, delta, gamma"
```

#### Iterating Over Tables

```lua
-- Iterate over an array
local colors = {"red", "green", "blue"}
for index, value in ipairs(colors) do
    print(index, value)
    -- 1 red
    -- 2 green
    -- 3 blue
end

-- Iterate over entire table
local player = {
    name = "Arthas",
    level = 80,
    class = "Death Knight",
    [1] = "first"
}

for key, value in pairs(player) do
    print(key, value)
    -- name    Arthas
    -- level   80
    -- class   Death Knight
    -- 1       first
end
```

#### Sorting Complex Tables

```lua
local guild = {
    {name = "Cladhaire", level = 80},
    {name = "Deathsquid", level = 68},
    {name = "Draoi", level = 80}
}

-- Comparison function
local function sortByName(a, b)
    return a.name < b.name
end

table.sort(guild, sortByName)

for idx, member in ipairs(guild) do
    print(member.name, member.level)
end
-- Cladhaire  80
-- Deathsquid 68
-- Draoi      80

-- Complex sort: by level then by name
local function sortByLevelAndName(a, b)
    if a.level == b.level then
        return a.name < b.name
    end
    return a.level < b.level
end

table.sort(guild, sortByLevelAndName)
```

### Chapter 5: Advanced Functions

#### Multiple Returns

```lua
-- Function with multiple returns
function getPosition()
    return 100, 200
end

local x, y = getPosition()
print(x, y)  -- 100  200

-- Ignore some returns
local x = getPosition()  -- x = 100, y ignored

-- Dummy variable to ignore
local _, y = getPosition()  -- x ignored, y = 200
```

#### Variadic Functions (arg table - Lua 5.0)

```lua
-- Accept variable number of arguments
-- In Lua 5.0, 'arg' is an automatic table
function sum()
    local total = 0
    -- arg.n contains the number of arguments
    for i = 1, arg.n do
        total = total + arg[i]
    end
    return total
end

print(sum(1, 2, 3, 4, 5))  -- 15

-- Alternative with ipairs
function sumAlt()
    local total = 0
    for i, value in ipairs(arg) do
        total = total + value
    end
    return total
end

-- Note Lua 5.0: arg is available automatically in functions
-- arg[1], arg[2], ... = arguments
-- arg.n = number of arguments
```

### Chapter 6: Lua Standard Libraries

#### String Library

```lua
-- string.format (printf-like)
local msg = string.format("Hello %s, you are level %d", "Arthas", 80)
-- "Hello Arthas, you are level 80"

local gold = 1234.56
print(string.format("%.2f gold", gold))  -- "1234.56 gold"
print(string.format("%d gold", gold))    -- "1234 gold"

-- Patterns (regex-like)
local text = "Hello World 123"

-- Find
local start, finish = string.find(text, "World")  -- 7, 11

-- Extract
local match = string.match(text, "%d+")  -- "123"

-- Replace
local result = string.gsub(text, "World", "Azeroth")
-- "Hello Azeroth 123"

-- Iterate over matches
for word in string.gmatch(text, "%a+") do
    print(word)
    -- Hello
    -- World
end
```

**Character Classes**:
```
%a  - Letter
%c  - Control character
%d  - Digit
%l  - Lowercase letter
%p  - Punctuation
%s  - Space
%u  - Uppercase letter
%w  - Alphanumeric
%x  - Hexadecimal digit
%z  - Null character (\0)

Uppercase = complement (e.g., %D = non-digit)
```

#### Math Library

```lua
math.abs(-42)           -- 42
math.ceil(3.2)          -- 4
math.floor(3.8)         -- 3
math.max(1, 5, 3)       -- 5
math.min(1, 5, 3)       -- 1
math.sqrt(16)           -- 4
math.pow(2, 3)          -- 8
math.random()           -- Random number [0, 1)
math.random(10)         -- Random number [1, 10]
math.random(5, 10)      -- Random number [5, 10]

-- Constants
math.pi                 -- 3.14159...
math.huge               -- Infinity
```

---

## Part II: Programming in WoW

### Chapter 8: Anatomy of an Addon

#### Complete TOC File

```toc
## Interface: 30300
## Title: My Awesome Addon
## Notes: Does awesome things
## Author: YourName
## Version: 1.0.0
## DefaultState: enabled
## SavedVariables: MyAddonDB
## SavedVariablesPerCharacter: MyAddonCharDB
## Dependencies: Blizzard_CombatLog
## OptionalDeps: LibStub, CallbackHandler-1.0
## LoadOnDemand: 0
## X-Category: Combat
## X-Website: https://example.com
## X-Email: you@example.com

# Libraries
Libs\LibStub.lua

# Localization
Locales\enUS.lua
Locales\frFR.lua

# Core
Core.lua

# UI
Templates.xml
MainFrame.xml
MainFrame.lua
```

**Important Directives**:
- `## Interface`: Client version (11200 = 1.12, 30300 = 3.3)
- `## SavedVariables`: Global persistent variables
- `## SavedVariablesPerCharacter`: Per-character variables
- `## Dependencies`: Required addons
- `## OptionalDeps`: Optional addons
- `## LoadOnDemand`: Load on demand (1) or at startup (0)

#### Localization

```lua
-- Locales/enUS.lua
local L = {}
L["ADDON_LOADED"] = "Addon loaded successfully!"
L["BUTTON_CLOSE"] = "Close"
L["SETTINGS"] = "Settings"

-- Set as global
MyAddon_Locale = L

-- Locales/frFR.lua
if GetLocale() ~= "frFR" then return end

local L = MyAddon_Locale
L["ADDON_LOADED"] = "Addon chargé avec succès!"
L["BUTTON_CLOSE"] = "Fermer"
L["SETTINGS"] = "Paramètres"

-- Usage
local L = MyAddon_Locale
print(L["ADDON_LOADED"])
```

### Chapter 9: Frames, Widgets and UI Elements

#### Creating a Frame in XML

```xml
<Ui xmlns="http://www.blizzard.com/wow/ui/"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.blizzard.com/wow/ui/
    ..\FrameXML\UI.xsd">

    <Frame name="MyAddonFrame" parent="UIParent" hidden="false">
        <Size>
            <AbsDimension x="300" y="200"/>
        </Size>
        <Anchors>
            <Anchor point="CENTER"/>
        </Anchors>

        <Backdrop bgFile="Interface\DialogFrame\UI-DialogBox-Background"
                  edgeFile="Interface\DialogFrame\UI-DialogBox-Border"
                  tile="true">
            <BackgroundInsets>
                <AbsInset left="11" right="12" top="12" bottom="11"/>
            </BackgroundInsets>
            <TileSize>
                <AbsValue val="32"/>
            </TileSize>
            <EdgeSize>
                <AbsValue val="32"/>
            </EdgeSize>
        </Backdrop>

        <Layers>
            <Layer level="ARTWORK">
                <FontString name="$parentTitle" inherits="GameFontNormal">
                    <Anchors>
                        <Anchor point="TOP">
                            <Offset>
                                <AbsDimension x="0" y="-15"/>
                            </Offset>
                        </Anchor>
                    </Anchors>
                </FontString>
            </Layer>
        </Layers>

        <Scripts>
            <OnLoad>
                self:RegisterEvent("PLAYER_LOGIN");
                getglobal(self:GetName().."Title"):SetText("My Addon");
            </OnLoad>
            <OnEvent>
                if event == "PLAYER_LOGIN" then
                    DEFAULT_CHAT_FRAME:AddMessage("Addon loaded!");
                end
            </OnEvent>
        </Scripts>
    </Frame>
</Ui>
```

#### Creating a Frame in Lua

```lua
-- Create a frame
local frame = CreateFrame("Frame", "MyLuaFrame", UIParent)
frame:SetSize(300, 200)
frame:SetPoint("CENTER")

-- Backdrop
frame:SetBackdrop({
    bgFile = "Interface\\DialogFrame\\UI-DialogBox-Background",
    edgeFile = "Interface\\DialogFrame\\UI-DialogBox-Border",
    tile = true,
    tileSize = 32,
    edgeSize = 32,
    insets = {left = 11, right = 12, top = 12, bottom = 11}
})

-- Texture
local texture = frame:CreateTexture(nil, "ARTWORK")
texture:SetTexture("Interface\\Icons\\INV_Misc_QuestionMark")
texture:SetSize(64, 64)
texture:SetPoint("CENTER")

-- FontString
local text = frame:CreateFontString(nil, "OVERLAY", "GameFontNormal")
text:SetPoint("TOP", 0, -15)
text:SetText("My Lua Frame")

-- Make draggable
frame:SetMovable(true)
frame:EnableMouse(true)
frame:RegisterForDrag("LeftButton")
frame:SetScript("OnDragStart", frame.StartMoving)
frame:SetScript("OnDragStop", frame.StopMovingOrSizing)

-- Events
frame:RegisterEvent("PLAYER_LOGIN")
frame:SetScript("OnEvent", function(self, event, ...)
    if event == "PLAYER_LOGIN" then
        DEFAULT_CHAT_FRAME:AddMessage("Lua frame loaded!")
    end
end)
```

#### Anchors (Anchor Points)

```lua
-- Simple point
frame:SetPoint("CENTER")  -- Screen center

-- Point with offset
frame:SetPoint("TOPLEFT", 10, -10)  -- 10px right, 10px down

-- Point relative to another frame
frame:SetPoint("LEFT", otherFrame, "RIGHT", 5, 0)
-- Left side of frame is 5px to the right of the right side of otherFrame

-- Multiple points (stretch)
frame:SetPoint("TOPLEFT", parent, "TOPLEFT", 10, -10)
frame:SetPoint("BOTTOMRIGHT", parent, "BOTTOMRIGHT", -10, 10)

-- Fill completely
frame:SetAllPoints(parent)
```

**Available Points**:
```
TOPLEFT      TOP      TOPRIGHT
LEFT         CENTER   RIGHT
BOTTOMLEFT   BOTTOM   BOTTOMRIGHT
```

### Chapter 10: Frame Templates

#### Defining a Template

```xml
<Button name="MyButtonTemplate" virtual="true">
    <Size>
        <AbsDimension x="100" y="32"/>
    </Size>

    <NormalTexture file="Interface\Buttons\UI-Panel-Button-Up"/>
    <PushedTexture file="Interface\Buttons\UI-Panel-Button-Down"/>
    <HighlightTexture file="Interface\Buttons\UI-Panel-Button-Highlight"
                      alphaMode="ADD"/>

    <NormalFont style="GameFontNormal"/>
    <HighlightFont style="GameFontHighlight"/>
    <DisabledFont style="GameFontDisable"/>

    <Scripts>
        <OnClick>
            PlaySound("igMainMenuOptionCheckBoxOn");
        </OnClick>
    </Scripts>
</Button>
```

#### Using a Template

```xml
<!-- In XML -->
<Button name="MyButton1" inherits="MyButtonTemplate" parent="MyFrame">
    <Anchors>
        <Anchor point="TOP" relativePoint="TOP">
            <Offset><AbsDimension x="0" y="-30"/></Offset>
        </Anchor>
    </Anchors>
    <Scripts>
        <OnClick>
            print("Button 1 clicked!");
        </OnClick>
    </Scripts>
</Button>
```

```lua
-- In Lua
local button = CreateFrame("Button", "MyLuaButton", frame, "MyButtonTemplate")
button:SetPoint("BOTTOM", 0, 10)
button:SetText("Click Me")
button:SetScript("OnClick", function(self)
    print("Lua button clicked!")
end)
```

#### $parent and parentKey

```xml
<!-- $parent will be replaced by parent's name -->
<Frame name="MyMainFrame">
    <Frames>
        <Button name="$parentCloseButton">
            <!-- Real name: "MyMainFrameCloseButton" -->
        </Button>
    </Frames>
</Frame>

<!-- parentKey creates automatic reference -->
<Frame name="MyFrame">
    <Frames>
        <Button name="$parentButton" parentKey="closeBtn">
            <!-- Accessible via: MyFrame.closeBtn -->
        </Button>
    </Frames>
</Frame>
```

### Chapter 11: World of Warcraft API

#### API Categories

1. **Normal APIs**: Native C functions
2. **Library-like APIs**: Groups of related functions
3. **FrameXML Functions**: Blizzard Lua functions
4. **Protected Functions**: Requires Blizzard signature

#### Example: Scanning Bags

```lua
-- Scan all bags
function ScanAllBags()
    local items = {}

    -- Backpack (0) + 4 bags (1-4)
    for bag = 0, 4 do
        local numSlots = GetContainerNumSlots(bag)

        for slot = 1, numSlots do
            local texture, count, locked, quality, readable, lootable,
                  link = GetContainerItemInfo(bag, slot)

            if link then
                table.insert(items, {
                    bag = bag,
                    slot = slot,
                    link = link,
                    count = count or 1,
                    quality = quality,
                    texture = texture
                })
            end
        end
    end

    return items
end

-- Usage
local allItems = ScanAllBags()
for _, item in ipairs(allItems) do
    print(item.link, "x" .. item.count)
end
```

### Chapter 12: Interacting with Widgets

#### Button Scripts

```lua
local button = CreateFrame("Button", "MyButton", UIParent)
button:SetSize(100, 32)
button:SetPoint("CENTER")

-- Textures
button:SetNormalTexture("Interface\\Buttons\\UI-Panel-Button-Up")
button:SetPushedTexture("Interface\\Buttons\\UI-Panel-Button-Down")
button:SetHighlightTexture("Interface\\Buttons\\UI-Panel-Button-Highlight")

-- Text
button:SetText("Click Me")

-- OnClick
button:SetScript("OnClick", function(self, button, down)
    if button == "LeftButton" then
        print("Left clicked!")
    elseif button == "RightButton" then
        print("Right clicked!")
    end
end)

-- OnEnter (tooltip)
button:SetScript("OnEnter", function(self)
    GameTooltip:SetOwner(self, "ANCHOR_RIGHT")
    GameTooltip:SetText("My Button")
    GameTooltip:AddLine("Click to do something", 1, 1, 1)
    GameTooltip:Show()
end)

-- OnLeave
button:SetScript("OnLeave", function(self)
    GameTooltip:Hide()
end)

-- Enable/Disable
button:Enable()
button:Disable()
```

#### EditBox (Text Field)

```lua
local editbox = CreateFrame("EditBox", "MyEditBox", UIParent)
editbox:SetSize(200, 32)
editbox:SetPoint("CENTER")
editbox:SetFontObject(ChatFontNormal)
editbox:SetAutoFocus(false)

-- Backdrop
editbox:SetBackdrop({
    bgFile = "Interface\\DialogFrame\\UI-DialogBox-Background",
    edgeFile = "Interface\\DialogFrame\\UI-DialogBox-Border",
    tile = true, tileSize = 32, edgeSize = 32,
    insets = {left = 11, right = 12, top = 12, bottom = 11}
})

-- Scripts
editbox:SetScript("OnEnterPressed", function(self)
    local text = self:GetText()
    print("You entered:", text)
    self:ClearFocus()
end)

editbox:SetScript("OnEscapePressed", function(self)
    self:ClearFocus()
end)

editbox:SetScript("OnEditFocusGained", function(self)
    self:HighlightText()
end)

editbox:SetScript("OnEditFocusLost", function(self)
    self:HighlightText(0, 0)
end)
```

### Chapter 13: Responding to Events

#### Clean Event Handler

```lua
local addon = CreateFrame("Frame")
local events = {}

-- Event functions
function events:PLAYER_LOGIN()
    print("Player logged in!")
    self:InitializeAddon()
end

function events:PLAYER_ENTERING_WORLD()
    print("Entering world...")
end

function events:BAG_UPDATE(bagID)
    print("Bag updated:", bagID)
    self:ScanBags()
end

-- Dispatcher
addon:SetScript("OnEvent", function(self, event, ...)
    if events[event] then
        events[event](self, ...)
    end
end)

-- Register all events
for event in pairs(events) do
    addon:RegisterEvent(event)
end

-- Addon methods
function addon:InitializeAddon()
    -- Initialization
end

function addon:ScanBags()
    -- Scan bags
end
```

#### SavedVariables

```lua
-- In TOC
## SavedVariables: MyAddonDB
## SavedVariablesPerCharacter: MyAddonCharDB

-- In code
local defaults = {
    enabled = true,
    scale = 1.0,
    position = {x = 0, y = 0}
}

function events:ADDON_LOADED(addonName)
    if addonName ~= "MyAddon" then return end

    -- Initialize global DB
    if not MyAddonDB then
        MyAddonDB = {}
    end

    -- Apply defaults
    for k, v in pairs(defaults) do
        if MyAddonDB[k] == nil then
            if type(v) == "table" then
                MyAddonDB[k] = CopyTable(v)
            else
                MyAddonDB[k] = v
            end
        end
    end

    -- Per-character DB
    if not MyAddonCharDB then
        MyAddonCharDB = {}
    end

    print("Addon loaded and configured!")
end
```

### Chapter 14: CombatTracker Project

#### Complete Addon Structure

```lua
-- CombatTracker.lua
local addon = CreateFrame("Frame", "CombatTrackerFrame", UIParent)
addon:SetSize(200, 50)
addon:SetPoint("CENTER")

-- Backdrop
addon:SetBackdrop({
    bgFile = "Interface\\DialogFrame\\UI-DialogBox-Background",
    edgeFile = "Interface\\DialogFrame\\UI-DialogBox-Border",
    tile = true, tileSize = 32, edgeSize = 32,
    insets = {left = 11, right = 12, top = 12, bottom = 11}
})

-- FontString
local text = addon:CreateFontString(nil, "OVERLAY", "GameFontNormalLarge")
text:SetPoint("CENTER")
text:SetText("0 DPS")
addon.text = text

-- Variables
local damage = 0
local startTime = nil

-- Functions
local function UpdateText()
    if startTime then
        local elapsed = GetTime() - startTime
        local dps = damage / elapsed
        addon.text:SetText(string.format("%.0f DPS", dps))
    else
        addon.text:SetText("0 DPS")
    end
end

local function ResetCombat()
    damage = 0
    startTime = nil
    UpdateText()
end

local function ReportDPS()
    if damage > 0 and startTime then
        local elapsed = GetTime() - startTime
        local dps = damage / elapsed
        SendChatMessage(
            string.format("DPS: %.0f (%.0f damage in %.0fs)",
                          dps, damage, elapsed),
            "SAY"
        )
    end
end

-- Events
local events = {}

function events:PLAYER_REGEN_DISABLED()
    -- Combat starts
    if not startTime then
        startTime = GetTime()
        damage = 0
    end
end

function events:PLAYER_REGEN_ENABLED()
    -- Combat ends
    UpdateText()
    -- Auto-reset after 5 seconds
    C_Timer.After(5, ResetCombat)
end

function events:UNIT_COMBAT(unitID, action, ...)
    if unitID ~= "player" then return end

    local damageStr, damageType = ...
    if action == "WOUND" and damageStr then
        damage = damage + tonumber(damageStr)
        UpdateText()
    end
end

-- Dispatcher
addon:SetScript("OnEvent", function(self, event, ...)
    if events[event] then
        events[event](self, ...)
    end
end)

for event in pairs(events) do
    addon:RegisterEvent(event)
end

-- Make draggable
addon:SetMovable(true)
addon:EnableMouse(true)
addon:RegisterForDrag("LeftButton")
addon:SetScript("OnDragStart", addon.StartMoving)
addon:SetScript("OnDragStop", addon.StopMovingOrSizing)

-- Right-click to report
addon:SetScript("OnMouseUp", function(self, button)
    if button == "RightButton" then
        ReportDPS()
    end
end)
```

---

## Part III: Advanced Techniques

### Chapter 17: Slash Commands

#### Simple Command

```lua
SLASH_MYADDON1 = "/myaddon"
SLASH_MYADDON2 = "/ma"

SlashCmdList["MYADDON"] = function(msg)
    msg = msg:lower():trim()

    if msg == "" or msg == "help" then
        print("MyAddon commands:")
        print("/myaddon show - Show frame")
        print("/myaddon hide - Hide frame")
        print("/myaddon reset - Reset settings")
    elseif msg == "show" then
        MyAddonFrame:Show()
    elseif msg == "hide" then
        MyAddonFrame:Hide()
    elseif msg == "reset" then
        MyAddonDB = nil
        ReloadUI()
    else
        print("Unknown command. Type /myaddon help")
    end
end
```

#### Commands with Arguments

```lua
local function HandleCommand(msg)
    -- Extract command and arguments
    local cmd, arg = msg:match("^(%S*)%s*(.-)$")
    cmd = cmd:lower()

    if cmd == "scale" then
        local scale = tonumber(arg)
        if scale and scale >= 0.5 and scale <= 2.0 then
            MyAddonFrame:SetScale(scale)
            MyAddonDB.scale = scale
            print("Scale set to", scale)
        else
            print("Invalid scale. Use 0.5 to 2.0")
        end

    elseif cmd == "color" then
        local r, g, b = arg:match("(%d+)%s+(%d+)%s+(%d+)")
        if r and g and b then
            r, g, b = r/255, g/255, b/255
            MyAddonFrame:SetBackdropColor(r, g, b)
            print(string.format("Color set to RGB(%d, %d, %d)", r*255, g*255, b*255))
        else
            print("Usage: /myaddon color <r> <g> <b>")
        end

    else
        print("Unknown command")
    end
end

SLASH_MYADDON1 = "/myaddon"
SlashCmdList["MYADDON"] = HandleCommand
```

### Chapter 18: OnUpdate

#### OnUpdate Throttling

```lua
local frame = CreateFrame("Frame")
local elapsed = 0
local updateInterval = 0.5  -- 2 times per second

frame:SetScript("OnUpdate", function(self, delta)
    elapsed = elapsed + delta

    if elapsed >= updateInterval then
        -- Code executed every 0.5 seconds
        self:UpdateData()
        elapsed = 0
    end
end)

function frame:UpdateData()
    -- Expensive logic
end
```

#### Simple Timer

```lua
local function CreateTimer(duration, callback)
    local frame = CreateFrame("Frame")
    local elapsed = 0

    frame:SetScript("OnUpdate", function(self, delta)
        elapsed = elapsed + delta

        if elapsed >= duration then
            self:SetScript("OnUpdate", nil)
            callback()
        end
    end)

    return frame
end

-- Usage
CreateTimer(5, function()
    print("5 seconds elapsed!")
end)
```

### Chapter 19: Function Hooking

#### Basic Hook

```lua
-- Save original function
local original_GetItemInfo = GetItemInfo

-- Replace with new function
GetItemInfo = function(...)
    -- Code before
    print("GetItemInfo called")

    -- Call original
    local name, link, quality, iLevel, reqLevel, class, subclass, maxStack,
          equipSlot, texture, vendorPrice = original_GetItemInfo(...)

    -- Code after
    if name then
        print("Item:", name)
    end

    -- Return values
    return name, link, quality, iLevel, reqLevel, class, subclass, maxStack,
           equipSlot, texture, vendorPrice
end
```

#### Secure Hook

```lua
-- Pre hook (before original)
hooksecurefunc("UseAction", function(slot, checkCursor, onSelf)
    print("Action used:", slot)
end)

-- Frame script hook
local original_OnClick = MyButton:GetScript("OnClick")

MyButton:SetScript("OnClick", function(self, button, down)
    print("Button clicked!")

    if original_OnClick then
        original_OnClick(self, button, down)
    end
end)

-- Or with HookScript
MyButton:HookScript("OnClick", function(self, button, down)
    print("Hooked click!")
end)
```

### Chapter 21: Combat Log

#### Parsing the Combat Log

```lua
local frame = CreateFrame("Frame")
frame:RegisterEvent("COMBAT_LOG_EVENT_UNFILTERED")

local function ParseCombatLog(...)
    local timestamp, event, _, sourceGUID, sourceName, sourceFlags,
          destGUID, destName, destFlags = ...

    -- Ignore if not from player
    if sourceGUID ~= UnitGUID("player") then return end

    -- SWING_DAMAGE
    if event == "SWING_DAMAGE" then
        local amount, overkill, school, resisted, blocked, absorbed,
              critical, glancing, crushing = select(9, ...)
        print(string.format("Hit %s for %d damage", destName, amount))

    -- SPELL_DAMAGE
    elseif event == "SPELL_DAMAGE" then
        local spellId, spellName, spellSchool = select(9, ...)
        local amount, overkill, school, resisted, blocked, absorbed,
              critical, glancing, crushing = select(12, ...)
        print(string.format("%s hit %s for %d damage",
                           spellName, destName, amount))

    -- SPELL_HEAL
    elseif event == "SPELL_HEAL" then
        local spellId, spellName, spellSchool = select(9, ...)
        local amount, overhealing, absorbed, critical = select(12, ...)
        print(string.format("%s healed %s for %d",
                           spellName, destName, amount))
    end
end

frame:SetScript("OnEvent", function(self, event, ...)
    ParseCombatLog(...)
end)
```

---

## Key Code Examples

### Example 1: Complete Cooldown Tracker

```lua
local CooldownTracker = CreateFrame("Frame", "CooldownTrackerFrame", UIParent)
CooldownTracker:SetSize(200, 300)
CooldownTracker:SetPoint("RIGHT", -50, 0)

-- Backdrop
CooldownTracker:SetBackdrop({
    bgFile = "Interface\\DialogFrame\\UI-DialogBox-Background",
    edgeFile = "Interface\\DialogFrame\\UI-DialogBox-Border",
    tile = true, tileSize = 32, edgeSize = 32,
    insets = {left = 11, right = 12, top = 12, bottom = 11}
})

-- Title
local title = CooldownTracker:CreateFontString(nil, "OVERLAY", "GameFontNormalLarge")
title:SetPoint("TOP", 0, -15)
title:SetText("Cooldowns")

-- Cooldown tracking
local cooldowns = {}
local bars = {}

-- Create a cooldown bar
local function CreateCooldownBar(spell, duration)
    local bar = CreateFrame("StatusBar", nil, CooldownTracker)
    bar:SetSize(180, 20)
    bar:SetStatusBarTexture("Interface\\TargetingFrame\\UI-StatusBar")
    bar:SetStatusBarColor(0.2, 0.6, 1.0)
    bar:SetMinMaxValues(0, duration)
    bar:SetValue(duration)

    -- Text
    local text = bar:CreateFontString(nil, "OVERLAY", "GameFontNormalSmall")
    text:SetPoint("CENTER")
    text:SetText(spell)
    bar.text = text

    -- Position
    local yOffset = -40 - (table.getn(bars) * 25)
    bar:SetPoint("TOP", 0, yOffset)

    -- Data
    bar.spell = spell
    bar.startTime = GetTime()
    bar.duration = duration

    table.insert(bars, bar)
    return bar
end

-- Update bars
local function UpdateBars(elapsed)
    local now = GetTime()
    local activeBars = {}

    for i, bar in ipairs(bars) do
        local remaining = bar.duration - (now - bar.startTime)

        if remaining > 0 then
            bar:SetValue(remaining)
            bar.text:SetText(string.format("%s (%.1fs)", bar.spell, remaining))
            table.insert(activeBars, bar)
        else
            bar:Hide()
        end
    end

    -- Reorganize active bars
    for i, bar in ipairs(activeBars) do
        local yOffset = -40 - ((i-1) * 25)
        bar:SetPoint("TOP", 0, yOffset)
        bar:Show()
    end

    bars = activeBars
end

-- OnUpdate
CooldownTracker:SetScript("OnUpdate", UpdateBars)

-- Detect cooldowns
CooldownTracker:RegisterEvent("SPELL_UPDATE_COOLDOWN")
CooldownTracker:SetScript("OnEvent", function(self, event)
    local i = 1
    while true do
        local name, rank = GetSpellName(i, BOOKTYPE_SPELL)
        if not name then break end

        local start, duration = GetSpellCooldown(i, BOOKTYPE_SPELL)

        if duration > 1.5 then  -- Ignore GCD
            local key = name .. (rank or "")
            if not cooldowns[key] then
                cooldowns[key] = true
                CreateCooldownBar(name, duration)

                -- Clean up after expiration
                C_Timer.After(duration, function()
                    cooldowns[key] = nil
                end)
            end
        end

        i = i + 1
    end
end)
```

---

## Resources and Tools

### Development Tools

1. **WowLua**: In-game Lua console
2. **TexBrowser**: Texture browser
3. **ArtBrowser**: http://wowprogramming.com
4. **DevTools**: Blizzard debug tools

### Important Websites

- **WoWProgramming.com**: Documentation and tutorials
- **WoWInterface.com**: Addon repository
- **WoWWiki**: API documentation (archive)
- **GitHub**: Source code of popular addons

### Popular Addons to Study

1. **TomTom**: Navigation
2. **Clique**: Click casting
3. **PerfectRaid**: Raid frames
4. **LightHeaded**: Quest database
5. **Feed-O-Matic**: Pet feeding

### Debug Commands

```lua
-- Enable taint log
/console taintLog 1

-- Event trace
/eventtrace

-- Frame stack
/fstack

-- Dump frame info
/framestack

-- Reload UI
/reload

-- Script errors
/console scriptErrors 1
```

### Useful Snippets

```lua
-- Deep copy table
function CopyTable(src, dest)
    dest = dest or {}
    for k, v in pairs(src) do
        if type(v) == "table" then
            dest[k] = CopyTable(v)
        else
            dest[k] = v
        end
    end
    return dest
end

-- Format money
function FormatMoney(copper)
    local gold = floor(copper / 10000)
    local silver = floor((copper % 10000) / 100)
    local copper = copper % 100
    return string.format("%dg %ds %dc", gold, silver, copper)
end

-- Quality color
function GetQualityColor(quality)
    local color = ITEM_QUALITY_COLORS[quality]
    return color.r, color.g, color.b
end

-- Short number
function ShortNumber(num)
    if num >= 1000000 then
        return string.format("%.1fM", num/1000000)
    elseif num >= 1000 then
        return string.format("%.1fK", num/1000)
    else
        return tostring(num)
    end
end
```

---

## Conclusion

This book is THE reference for WoW addon programming. It covers:

✅ **Lua Fundamentals**: Everything you need to know
✅ **WoW Interface**: Frames, widgets, events
✅ **Advanced Techniques**: Combat log, secure templates, hooks
✅ **Practical Projects**: BagBuddy, CombatTracker
✅ **Complete Reference**: API, widgets, events

**To go further**:
1. Read the complete book (62k lines!)
2. Study Blizzard's FrameXML code
3. Analyze open-source addons
4. Practice with small projects
5. Share and contribute to the community

---

*Summary based on "World of Warcraft Programming: A Guide and Reference for Creating WoW Addons, 2nd Edition"*
*By James Whitehead II (Cladhaire) and Rick Roe (Gazmik Fizzwidget)*
*Published by Wiley Publishing, Inc. - 2010*
