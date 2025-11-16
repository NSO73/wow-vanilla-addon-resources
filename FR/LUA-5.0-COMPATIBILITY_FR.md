# ⚠️ IMPORTANT: Lua 5.0 vs Lua 5.1+ - Corrections pour WoW Vanilla 1.12

**WoW Vanilla 1.12 utilise Lua 5.0**, pas Lua 5.1+. Plusieurs fonctionnalités modernes n'existent pas.

---

## ❌ Fonctionnalités Lua 5.1+ à NE PAS Utiliser

### 1. Opérateur `#` pour la longueur

```lua
-- ❌ LUA 5.1+ (NE FONCTIONNE PAS)
local len = #myTable
local strLen = #myString

-- ✅ LUA 5.0 (VANILLA 1.12)
local len = table.getn(myTable)   -- Pour tables
local len = getn(myTable)         -- Version globale
local strLen = string.len(myString) -- Pour strings
```

### 2. Fonction `select()`

```lua
-- ❌ LUA 5.1+ (NE FONCTIONNE PAS)
function foo(...)
    for i = 1, select("#", ...) do
        local value = select(i, ...)
        print(value)
    end
end

-- ✅ LUA 5.0 (VANILLA 1.12)
function foo()
    -- arg est une table automatique en Lua 5.0
    for i = 1, arg.n do
        local value = arg[i]
        print(value)
    end
end
```

### 3. Table literal `{...}` pour varargs

```lua
-- ❌ LUA 5.1+ (NE FONCTIONNE PAS)
function foo(...)
    local args = {...}
    for i, v in ipairs(args) do
        print(i, v)
    end
end

-- ✅ LUA 5.0 (VANILLA 1.12)
function foo()
    -- arg existe automatiquement
    for i = 1, arg.n do
        print(i, arg[i])
    end
end
```

### 4. Paramètre `...` dans signature (limité)

```lua
-- ⚠️ LUA 5.0 : ... existe MAIS limité
function foo(...)
    -- ... peut être utilisé MAIS pas comme en 5.1+
    -- Préférer arg
end

-- ✅ LUA 5.0 (MEILLEURE PRATIQUE)
function foo()
    -- arg est automatiquement disponible
    -- arg.n = nombre d'arguments
    -- arg[1], arg[2], etc.
end
```

### 5. Événements WoW et varargs

```lua
-- ❌ MODERNE (peut causer confusion)
frame:SetScript("OnEvent", function(self, event, ...)
    if event == "PLAYER_LEVEL_UP" then
        local newLevel = ...  -- Confus!
    end
end)

-- ✅ VANILLA 1.12 (CLAIR)
frame:SetScript("OnEvent", function()
    if event == "PLAYER_LEVEL_UP" then
        local newLevel = arg1  -- arg1, arg2, etc. existent
    end
end)

-- OU (si besoin de self)
frame:SetScript("OnEvent", function(self, event)
    if event == "PLAYER_LEVEL_UP" then
        local newLevel = arg1
    end
end)
```

---

## ✅ Syntaxe Correcte pour Lua 5.0 (Vanilla 1.12)

### Varargs - Table `arg`

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

### Longueur de table

```lua
local t = {"a", "b", "c"}

-- Méthode 1: table.getn()
local len = table.getn(t)  -- 3

-- Méthode 2: getn() globale
local len = getn(t)  -- 3

-- Méthode 3: Boucle manuelle (si table sparse)
local len = 0
for i, v in ipairs(t) do
    len = i
end
```

### Événements WoW

```lua
local frame = CreateFrame("Frame")

frame:RegisterEvent("PLAYER_LOGIN")
frame:RegisterEvent("PLAYER_LEVEL_UP")
frame:RegisterEvent("QUEST_LOG_UPDATE")

frame:SetScript("OnEvent", function()
    -- event est global dans le contexte
    -- arg1, arg2, ... sont disponibles selon l'événement

    if event == "PLAYER_LOGIN" then
        print("Login!")

    elseif event == "PLAYER_LEVEL_UP" then
        local newLevel = arg1  -- Premier argument
        print("Level up! New level:", newLevel)

    elseif event == "QUEST_LOG_UPDATE" then
        print("Quest log updated")
    end
end)
```

### this vs self

```lua
-- WoW 1.12 utilise 'this' dans les handlers XML
-- C'est une référence globale au frame actuel

-- Dans un script XML:
<OnClick>
    print(this:GetName())  -- ✅ CORRECT
end)
</OnClick>

-- Dans du code Lua:
local function OnClick()
    print(this:GetName())  -- ✅ this existe dans les handlers
end

button:SetScript("OnClick", OnClick)
```

### Passer des arguments avec varargs

```lua
-- ❌ MODERNE
function dispatch(handler, ...)
    handler(...)  -- Peut ne pas fonctionner correctement
end

-- ✅ VANILLA 1.12
function dispatch(handler)
    handler(unpack(arg))  -- arg contient tous les arguments
end

-- Exemple d'utilisation
local function myHandler(a, b, c)
    print(a, b, c)
end

dispatch(myHandler, 1, 2, 3)  -- Affiche: 1  2  3
```

---

## 🔧 Corrections pour les Documents

### Document 1: WoW-Addon-Development-Reference.md

#### Ligne 967 - Namespace initialization
```lua
-- ❌ ACTUEL
local AddonName, Addon = ...

-- ✅ CORRIGÉ POUR VANILLA
-- Note: Cette syntaxe ne fonctionne PAS en Lua 5.0
-- Les fichiers .lua n'ont PAS accès à un ... automatique

-- Solution 1: Utiliser global directement
MonAddon = MonAddon or {}

-- Solution 2: Si vraiment besoin du nom
local addonName = "MonAddon"
local addon = _G[addonName] or {}
_G[addonName] = addon
```

#### Ligne 997-1000 - Event dispatcher
```lua
-- ❌ ACTUEL
frame:SetScript("OnEvent", function(self, event, ...)
    if events[event] then
        events[event](self, ...)
    end
end)

-- ✅ CORRIGÉ POUR VANILLA
frame:SetScript("OnEvent", function()
    if events[event] then
        -- Méthode 1: Passer arg directement
        events[event](this, arg1, arg2, arg3, arg4, arg5)

        -- Méthode 2: Utiliser unpack si nombre variable
        -- events[event](this, unpack(arg))
    end
end)
```

#### Ligne 1278 - Missing table check
```lua
-- ❌ ACTUEL
if #missing > 0 then

-- ✅ CORRIGÉ POUR VANILLA
if table.getn(missing) > 0 then
    -- OU
    if getn(missing) > 0 then
```

### Document 2: WoW-Programming-Guide-Summary.md

#### Ligne 274 - Table length
```lua
-- ❌ ACTUEL
print(#t)              -- 3

-- ✅ CORRIGÉ POUR VANILLA
print(table.getn(t))   -- 3
print(getn(t))         -- 3 (version globale)
```

#### Lignes 375-397 - Varargs section
```lua
-- ❌ ACTUEL (Section à réécrire)
#### Fonctions Variadiques (...)

function sum(...)
    for i = 1, select("#", ...) do
        local value = select(i, ...)
        total = total + value
    end
end

function sumAlt(...)
    local args = {...}
end

-- ✅ CORRIGÉ POUR VANILLA
#### Fonctions Variadiques (arg table)

-- En Lua 5.0, les fonctions variadiques utilisent la table 'arg'
function sum()
    local total = 0
    -- arg.n contient le nombre d'arguments
    for i = 1, arg.n do
        total = total + arg[i]
    end
    return total
end

print(sum(1, 2, 3, 4, 5))  -- 15

-- Alternative avec unpack
function sumAlt()
    local total = 0
    for i = 1, arg.n do
        total = total + arg[i]
    end
    return total
end
```

#### Lignes 1260-1291 - Combat Log Parser
```lua
-- ❌ ACTUEL
local function ParseCombatLog(...)
    local timestamp, event, _, sourceGUID, sourceName, sourceFlags,
          destGUID, destName, destFlags = ...

    local amount, overkill, school, resisted, blocked, absorbed,
          critical, glancing, crushing = select(9, ...)

    local spellId, spellName, spellSchool = select(9, ...)

-- ✅ CORRIGÉ POUR VANILLA
local function ParseCombatLog()
    -- Les arguments sont dans arg[1], arg[2], etc.
    local timestamp = arg[1]
    local event = arg[2]
    -- arg[3] est souvent nil
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
    ParseCombatLog()  -- arg est automatiquement disponible
end)
```

#### Ligne 1339 - Bars count
```lua
-- ❌ ACTUEL
local yOffset = -40 - (#bars * 25)

-- ✅ CORRIGÉ POUR VANILLA
local yOffset = -40 - (table.getn(bars) * 25)
-- OU
local yOffset = -40 - (getn(bars) * 25)
```