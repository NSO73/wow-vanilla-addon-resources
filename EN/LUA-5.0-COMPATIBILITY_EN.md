# ⚠️ IMPORTANT: Lua 5.0 vs Lua 5.1+ - Corrections for WoW Vanilla 1.12

**WoW Vanilla 1.12 uses Lua 5.0**, not Lua 5.1+. Several modern features don't exist.

---

## ❌ Lua 5.1+ Features NOT to Use

### 1. `#` Operator for Length

```lua
-- ❌ LUA 5.1+ (DOESN'T WORK)
local len = #myTable
local strLen = #myString

-- ✅ LUA 5.0 (VANILLA 1.12)
local len = table.getn(myTable)   -- For tables
local len = getn(myTable)         -- Global version
local strLen = string.len(myString) -- For strings
```

### 2. `select()` Function

```lua
-- ❌ LUA 5.1+ (DOESN'T WORK)
function foo(...)
    for i = 1, select("#", ...) do
        local value = select(i, ...)
        print(value)
    end
end

-- ✅ LUA 5.0 (VANILLA 1.12)
function foo()
    -- arg is an automatic table in Lua 5.0
    for i = 1, arg.n do
        local value = arg[i]
        print(value)
    end
end
```

### 3. Table Literal `{...}` for Varargs

```lua
-- ❌ LUA 5.1+ (DOESN'T WORK)
function foo(...)
    local args = {...}
    for i, v in ipairs(args) do
        print(i, v)
    end
end

-- ✅ LUA 5.0 (VANILLA 1.12)
function foo()
    -- arg exists automatically
    for i = 1, arg.n do
        print(i, arg[i])
    end
end
```

### 4. `...` Parameter in Signature (Limited)

```lua
-- ⚠️ LUA 5.0: ... exists BUT limited
function foo(...)
    -- ... can be used BUT not like in 5.1+
    -- Prefer arg
end

-- ✅ LUA 5.0 (BEST PRACTICE)
function foo()
    -- arg is automatically available
    -- arg.n = number of arguments
    -- arg[1], arg[2], etc.
end
```

### 5. WoW Events and Varargs

```lua
-- ❌ MODERN (can cause confusion)
frame:SetScript("OnEvent", function(self, event, ...)
    if event == "PLAYER_LEVEL_UP" then
        local newLevel = ...  -- Confusing!
    end
end)

-- ✅ VANILLA 1.12 (CLEAR)
frame:SetScript("OnEvent", function()
    if event == "PLAYER_LEVEL_UP" then
        local newLevel = arg1  -- arg1, arg2, etc. exist
    end
end)

-- OR (if self is needed)
frame:SetScript("OnEvent", function(self, event)
    if event == "PLAYER_LEVEL_UP" then
        local newLevel = arg1
    end
end)
```

---

## ✅ Correct Syntax for Lua 5.0 (Vanilla 1.12)

### Varargs - `arg` Table

```lua
function sum()
    local total = 0
    for i = 1, arg.n do
        total = total + arg[i]
    end
    return total
end

print(sum(1, 2, 3, 4, 5))  -- 15
```

### Table Length

```lua
local t = {"a", "b", "c"}

-- Method 1: table.getn()
local len = table.getn(t)  -- 3

-- Method 2: global getn()
local len = getn(t)  -- 3

-- Method 3: Manual loop (if sparse table)
local len = 0
for i, v in ipairs(t) do
    len = i
end
```

### WoW Events

```lua
local frame = CreateFrame("Frame")

frame:RegisterEvent("PLAYER_LOGIN")
frame:RegisterEvent("PLAYER_LEVEL_UP")
frame:RegisterEvent("QUEST_LOG_UPDATE")

frame:SetScript("OnEvent", function()
    -- event is global in context
    -- arg1, arg2, ... are available depending on event

    if event == "PLAYER_LOGIN" then
        print("Login!")

    elseif event == "PLAYER_LEVEL_UP" then
        local newLevel = arg1  -- First argument
        print("Level up! New level:", newLevel)

    elseif event == "QUEST_LOG_UPDATE" then
        print("Quest log updated")
    end
end)
```

### this vs self

```lua
-- WoW 1.12 uses 'this' in XML handlers
-- It's a global reference to the current frame

-- In an XML script:
<OnClick>
    print(this:GetName())  -- ✅ CORRECT
end)
</OnClick>

-- In Lua code:
local function OnClick()
    print(this:GetName())  -- ✅ this exists in handlers
end

button:SetScript("OnClick", OnClick)
```

### Passing Arguments with Varargs

```lua
-- ❌ MODERN
function dispatch(handler, ...)
    handler(...)  -- May not work correctly
end

-- ✅ VANILLA 1.12
function dispatch(handler)
    handler(unpack(arg))  -- arg contains all arguments
end

-- Usage example
local function myHandler(a, b, c)
    print(a, b, c)
end

dispatch(myHandler, 1, 2, 3)  -- Displays: 1  2  3
```

---

## 🔧 Corrections for Documents

### Document 1: WoW-Addon-Development-Reference.md

#### Line 967 - Namespace initialization
```lua
-- ❌ CURRENT
local AddonName, Addon = ...

-- ✅ CORRECTED FOR VANILLA
-- Note: This syntax does NOT work in Lua 5.0
-- .lua files do NOT have access to automatic ...

-- Solution 1: Use global directly
MyAddon = MyAddon or {}

-- Solution 2: If name is really needed
local addonName = "MyAddon"
local addon = _G[addonName] or {}
_G[addonName] = addon
```

#### Line 997-1000 - Event dispatcher
```lua
-- ❌ CURRENT
frame:SetScript("OnEvent", function(self, event, ...)
    if events[event] then
        events[event](self, ...)
    end
end)

-- ✅ CORRECTED FOR VANILLA
frame:SetScript("OnEvent", function()
    if events[event] then
        -- Method 1: Pass arg directly
        events[event](this, arg1, arg2, arg3, arg4, arg5)

        -- Method 2: Use unpack if variable number
        -- events[event](this, unpack(arg))
    end
end)
```

#### Line 1278 - Missing table check
```lua
-- ❌ CURRENT
if #missing > 0 then

-- ✅ CORRECTED FOR VANILLA
if table.getn(missing) > 0 then
    -- OR
    if getn(missing) > 0 then
```

### Document 2: WoW-Programming-Guide-Summary.md

#### Line 274 - Table length
```lua
-- ❌ CURRENT
print(#t)              -- 3

-- ✅ CORRECTED FOR VANILLA
print(table.getn(t))   -- 3
print(getn(t))         -- 3 (global version)
```

#### Lines 375-397 - Varargs section
```lua
-- ❌ CURRENT (Section to rewrite)
#### Variadic Functions (...)

function sum(...)
    for i = 1, select("#", ...) do
        local value = select(i, ...)
        total = total + value
    end
end

function sumAlt(...)
    local args = {...}
end

-- ✅ CORRECTED FOR VANILLA
#### Variadic Functions (arg table)

-- In Lua 5.0, variadic functions use the 'arg' table
function sum()
    local total = 0
    -- arg.n contains the number of arguments
    for i = 1, arg.n do
        total = total + arg[i]
    end
    return total
end

print(sum(1, 2, 3, 4, 5))  -- 15

-- Alternative with unpack
function sumAlt()
    local total = 0
    for i = 1, arg.n do
        total = total + arg[i]
    end
    return total
end
```

#### Lines 1260-1291 - Combat Log Parser
```lua
-- ❌ CURRENT
local function ParseCombatLog(...)
    local timestamp, event, _, sourceGUID, sourceName, sourceFlags,
          destGUID, destName, destFlags = ...

    local amount, overkill, school, resisted, blocked, absorbed,
          critical, glancing, crushing = select(9, ...)

    local spellId, spellName, spellSchool = select(9, ...)

-- ✅ CORRECTED FOR VANILLA
local function ParseCombatLog()
    -- Arguments are in arg[1], arg[2], etc.
    local timestamp = arg[1]
    local event = arg[2]
    -- arg[3] is often nil
    local sourceGUID = arg[4]
    local sourceName = arg[5]
    local sourceFlags = arg[6]
    local destGUID = arg[7]
    local destName = arg[8]
    local destFlags = arg[9]

    -- SWING_DAMAGE
    if event == "SWING_DAMAGE" then
        local amount = arg[10]
        local overkill = arg[11]
        local school = arg[12]
        local resisted = arg[13]
        local blocked = arg[14]
        local absorbed = arg[15]
        local critical = arg[16]
        local glancing = arg[17]
        local crushing = arg[18]

    -- SPELL_DAMAGE
    elseif event == "SPELL_DAMAGE" then
        local spellId = arg[10]
        local spellName = arg[11]
        local spellSchool = arg[12]
        local amount = arg[13]
        local overkill = arg[14]
        -- etc.
    end
end

frame:SetScript("OnEvent", function()
    ParseCombatLog()  -- arg is automatically available
end)
```

#### Line 1339 - Bars count
```lua
-- ❌ CURRENT
local yOffset = -40 - (#bars * 25)

-- ✅ CORRECTED FOR VANILLA
local yOffset = -40 - (table.getn(bars) * 25)
-- OR
local yOffset = -40 - (getn(bars) * 25)
```