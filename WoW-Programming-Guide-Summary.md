# Guide Pratique de Programmation WoW - Résumé du Livre

**Source**: World of Warcraft Programming: A Guide and Reference for Creating WoW Addons (2nd Edition)
**Auteurs**: James Whitehead II (Cladhaire) & Rick Roe (Gazmik Fizzwidget)
**Pages**: 62,000+ lignes de contenu technique

---

**⚠️ IMPORTANT - Lua 5.0 (WoW Vanilla 1.12)**

Ce guide est adapté pour **WoW Vanilla 1.12** qui utilise **Lua 5.0**, pas Lua 5.1+.

Différences critiques :
- ❌ Pas d'opérateur `#` → Utiliser `table.getn()` ou `getn()`
- ❌ Pas de `select()` → Utiliser `arg[n]` et `arg.n`
- ❌ Pas de `{...}` table literal → Utiliser table `arg` directement
- ✅ Varargs via `arg` table automatique
- ✅ `arg1`, `arg2`, etc. dans les événements WoW
- ✅ `this` dans les handlers (pas `self`)

Voir [LUA-5.0-COMPATIBILITY.md](LUA-5.0-COMPATIBILITY.md) pour détails complets.

---

## Table des Matières

1. [Structure du Livre](#structure-du-livre)
2. [Partie I: Apprendre à Programmer](#partie-i-apprendre-à-programmer)
3. [Partie II: Programmer dans WoW](#partie-ii-programmer-dans-wow)
4. [Partie III: Techniques Avancées](#partie-iii-techniques-avancées)
5. [Partie IV: Référence](#partie-iv-référence)
6. [Exemples de Code Clés](#exemples-de-code-clés)
7. [Ressources et Outils](#ressources-et-outils)

---

## Structure du Livre

### Organisation Générale

Le livre est divisé en 5 parties majeures :

- **Partie I** : Apprendre Lua et XML (Chapitres 1-7)
- **Partie II** : Programmation dans WoW (Chapitres 8-14)
- **Partie III** : Techniques Avancées (Chapitres 15-26)
- **Partie IV** : Référence (Chapitres 27-30)
- **Partie V** : Appendices (A-D)

### Public Cible

1. **Apprentis programmeurs** : Commencer par la Partie I
2. **Développeurs intermédiaires** : Survoler Partie I, se concentrer sur Partie II
3. **Programmeurs expérimentés** : Plonger directement dans Partie III

---

## Partie I: Apprendre à Programmer

### Chapitre 1: Programmation pour WoW

#### Qu'est-ce qu'un Addon ?

Un addon est un ensemble de fichiers qui étendent l'interface utilisateur de WoW :
- Fichiers `.lua` (logique/code)
- Fichiers `.xml` (interface graphique)
- Fichier `.toc` (métadonnées)
- Médias (textures, sons)

#### Créer son Premier Addon: HeyThere

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

### Chapitre 2: Bases de Lua

#### Types de Données

```lua
-- Nombres
local x = 42
local y = 3.14
local z = 1.5e10  -- Notation scientifique
local hex = 0xFF  -- Hexadécimal (255)

-- Chaînes
local str1 = "Hello"
local str2 = 'World'
local str3 = [[Multi-line
string]]

-- Booléens
local isTrue = true
local isFalse = false

-- Nil
local nothing = nil

-- Type d'une valeur
print(type(x))        -- "number"
print(type(str1))     -- "string"
print(type(isTrue))   -- "boolean"
print(type(nothing))  -- "nil"
```

#### Opérations sur les Chaînes

```lua
-- Concaténation
local full = "Hello" .. " " .. "World"  -- "Hello World"

-- Longueur
print(#"Hello")           -- 5
print(string.len("Hi"))   -- 2

-- Conversion
local num = tonumber("42")     -- 42
local str = tostring(42)       -- "42"

-- Comparaison
print("abc" < "def")      -- true
print("Apple" == "apple") -- false (sensible à la casse)
```

#### Opérateurs Logiques

```lua
-- AND : retourne le premier false/nil OU la dernière valeur
print(true and "yes")     -- "yes"
print(false and "yes")    -- false
print(nil and "yes")      -- nil

-- OR : retourne le premier true OU la dernière valeur
print(true or "no")       -- true
print(false or "yes")     -- "yes"
print(nil or "default")   -- "default"

-- NOT : négation
print(not true)           -- false
print(not nil)            -- true
print(not 0)              -- false (0 est true en Lua!)
```

### Chapitre 3: Fonctions et Structures de Contrôle

#### Définir des Fonctions

```lua
-- Fonction simple
function sayHello()
    print("Hello!")
end

-- Fonction avec arguments
function greet(name)
    print("Hello, " .. name)
end

-- Fonction avec retour
function add(a, b)
    return a + b
end

-- Fonctions locales (recommandé)
local function multiply(a, b)
    return a * b
end

-- Fonction anonyme
local square = function(x)
    return x * x
end
```

#### Structures Conditionnelles

```lua
-- If simple
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

-- Opérateur ternaire simulé
local status = (health > 50) and "Healthy" or "Wounded"
```

#### Boucles

```lua
-- While
local i = 1
while i <= 10 do
    print(i)
    i = i + 1
end

-- Repeat-until (équivalent do-while)
local i = 1
repeat
    print(i)
    i = i + 1
until i > 10

-- For numérique
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

### Chapitre 4: Tables

#### Création et Indexation

```lua
-- Table vide
local t = {}

-- Ajouter des éléments
t["key"] = "value"
t.name = "John"        -- Raccourci pour t["name"]
t[1] = "first"
t[2] = "second"

-- Accès
print(t["key"])        -- "value"
print(t.name)          -- "John"
print(t[1])            -- "first"

-- Table initialisée
local player = {
    name = "Arthas",
    level = 80,
    class = "Death Knight"
}

-- Array (tableau)
local colors = {"red", "green", "blue"}
print(colors[1])       -- "red" (les indices commencent à 1!)
```

#### Fonctions de Table

```lua
local t = {"alpha", "beta", "gamma"}

-- Longueur (Lua 5.0)
print(table.getn(t))   -- 3
print(getn(t))         -- 3 (version globale)

-- Insérer
table.insert(t, "delta")        -- Ajoute à la fin
table.insert(t, 2, "zeta")      -- Insère à la position 2

-- Retirer
local last = table.remove(t)    -- Retire le dernier
local second = table.remove(t, 2) -- Retire position 2

-- Concaténer
print(table.concat(t, ", "))    -- "alpha, beta, gamma"

-- Trier
table.sort(t)
print(table.concat(t, ", "))    -- "alpha, beta, delta, gamma"
```

#### Itération sur les Tables

```lua
-- Itérer sur un array
local colors = {"red", "green", "blue"}
for index, value in ipairs(colors) do
    print(index, value)
    -- 1 red
    -- 2 green
    -- 3 blue
end

-- Itérer sur toute la table
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

#### Trier des Tables Complexes

```lua
local guild = {
    {name = "Cladhaire", level = 80},
    {name = "Deathsquid", level = 68},
    {name = "Draoi", level = 80}
}

-- Fonction de comparaison
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

-- Tri complexe : par niveau puis par nom
local function sortByLevelAndName(a, b)
    if a.level == b.level then
        return a.name < b.name
    end
    return a.level < b.level
end

table.sort(guild, sortByLevelAndName)
```

### Chapitre 5: Fonctions Avancées

#### Retours Multiples

```lua
-- Fonction avec retours multiples
function getPosition()
    return 100, 200
end

local x, y = getPosition()
print(x, y)  -- 100  200

-- Ignorer certains retours
local x = getPosition()  -- x = 100, y ignoré

-- Variable dummy pour ignorer
local _, y = getPosition()  -- x ignoré, y = 200
```

#### Fonctions Variadiques (arg table - Lua 5.0)

```lua
-- Accepter un nombre variable d'arguments
-- En Lua 5.0, 'arg' est une table automatique
function sum()
    local total = 0
    -- arg.n contient le nombre d'arguments
    for i = 1, arg.n do
        total = total + arg[i]
    end
    return total
end

print(sum(1, 2, 3, 4, 5))  -- 15

-- Alternative avec ipairs
function sumAlt()
    local total = 0
    for i, value in ipairs(arg) do
        total = total + value
    end
    return total
end

-- Note Lua 5.0: arg est disponible automatiquement dans les fonctions
-- arg[1], arg[2], ... = arguments
-- arg.n = nombre d'arguments
```

### Chapitre 6: Bibliothèques Standard Lua

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

-- Trouver
local start, finish = string.find(text, "World")  -- 7, 11

-- Extraire
local match = string.match(text, "%d+")  -- "123"

-- Remplacer
local result = string.gsub(text, "World", "Azeroth")
-- "Hello Azeroth 123"

-- Itérer sur les matches
for word in string.gmatch(text, "%a+") do
    print(word)
    -- Hello
    -- World
end
```

**Classes de Caractères**:
```
%a  - Lettre
%c  - Caractère de contrôle
%d  - Chiffre
%l  - Lettre minuscule
%p  - Ponctuation
%s  - Espace
%u  - Lettre majuscule
%w  - Alphanumérique
%x  - Chiffre hexadécimal
%z  - Caractère nul (\0)

Majuscule = complément (ex: %D = non-chiffre)
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
math.random()           -- Nombre aléatoire [0, 1)
math.random(10)         -- Nombre aléatoire [1, 10]
math.random(5, 10)      -- Nombre aléatoire [5, 10]

-- Constantes
math.pi                 -- 3.14159...
math.huge               -- Infini
```

---

## Partie II: Programmer dans WoW

### Chapitre 8: Anatomie d'un Addon

#### Fichier TOC Complet

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

**Directives Importantes**:
- `## Interface` : Version du client (11200 = 1.12, 30300 = 3.3)
- `## SavedVariables` : Variables globales persistantes
- `## SavedVariablesPerCharacter` : Variables par personnage
- `## Dependencies` : Addons requis
- `## OptionalDeps` : Addons optionnels
- `## LoadOnDemand` : Charger à la demande (1) ou au démarrage (0)

#### Localisation

```lua
-- Locales/enUS.lua
local L = {}
L["ADDON_LOADED"] = "Addon loaded successfully!"
L["BUTTON_CLOSE"] = "Close"
L["SETTINGS"] = "Settings"

-- Définir comme global
MyAddon_Locale = L

-- Locales/frFR.lua
if GetLocale() ~= "frFR" then return end

local L = MyAddon_Locale
L["ADDON_LOADED"] = "Addon chargé avec succès!"
L["BUTTON_CLOSE"] = "Fermer"
L["SETTINGS"] = "Paramètres"

-- Utilisation
local L = MyAddon_Locale
print(L["ADDON_LOADED"])
```

### Chapitre 9: Frames, Widgets et Éléments Graphiques

#### Créer un Frame en XML

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

#### Créer un Frame en Lua

```lua
-- Créer un frame
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

-- Rendre déplaçable
frame:SetMovable(true)
frame:EnableMouse(true)
frame:RegisterForDrag("LeftButton")
frame:SetScript("OnDragStart", frame.StartMoving)
frame:SetScript("OnDragStop", frame.StopMovingOrSizing)

-- Événements
frame:RegisterEvent("PLAYER_LOGIN")
frame:SetScript("OnEvent", function(self, event, ...)
    if event == "PLAYER_LOGIN" then
        DEFAULT_CHAT_FRAME:AddMessage("Lua frame loaded!")
    end
end)
```

#### Anchors (Points d'Ancrage)

```lua
-- Point simple
frame:SetPoint("CENTER")  -- Centre de l'écran

-- Point avec offset
frame:SetPoint("TOPLEFT", 10, -10)  -- 10px droite, 10px bas

-- Point relatif à un autre frame
frame:SetPoint("LEFT", otherFrame, "RIGHT", 5, 0)
-- Le côté gauche de frame est à 5px à droite du côté droit de otherFrame

-- Points multiples (étirer)
frame:SetPoint("TOPLEFT", parent, "TOPLEFT", 10, -10)
frame:SetPoint("BOTTOMRIGHT", parent, "BOTTOMRIGHT", -10, 10)

-- Remplir complètement
frame:SetAllPoints(parent)
```

**Points disponibles**:
```
TOPLEFT      TOP      TOPRIGHT
LEFT         CENTER   RIGHT
BOTTOMLEFT   BOTTOM   BOTTOMRIGHT
```

### Chapitre 10: Templates de Frames

#### Définir un Template

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

#### Utiliser un Template

```xml
<!-- En XML -->
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
-- En Lua
local button = CreateFrame("Button", "MyLuaButton", frame, "MyButtonTemplate")
button:SetPoint("BOTTOM", 0, 10)
button:SetText("Click Me")
button:SetScript("OnClick", function(self)
    print("Lua button clicked!")
end)
```

#### $parent et parentKey

```xml
<!-- $parent sera remplacé par le nom du parent -->
<Frame name="MyMainFrame">
    <Frames>
        <Button name="$parentCloseButton">
            <!-- Nom réel : "MyMainFrameCloseButton" -->
        </Button>
    </Frames>
</Frame>

<!-- parentKey crée une référence automatique -->
<Frame name="MyFrame">
    <Frames>
        <Button name="$parentButton" parentKey="closeBtn">
            <!-- Accessible via: MyFrame.closeBtn -->
        </Button>
    </Frames>
</Frame>
```

### Chapitre 11: API World of Warcraft

#### Catégories d'API

1. **Normal APIs** : Fonctions C natives
2. **Library-like APIs** : Groupes de fonctions liées
3. **FrameXML Functions** : Fonctions Lua de Blizzard
4. **Protected Functions** : Requiert signature Blizzard

#### Exemple : Scanner les Sacs

```lua
-- Scanner tous les sacs
function ScanAllBags()
    local items = {}

    -- Sac à dos (0) + 4 sacs (1-4)
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

-- Utilisation
local allItems = ScanAllBags()
for _, item in ipairs(allItems) do
    print(item.link, "x" .. item.count)
end
```

### Chapitre 12: Interagir avec les Widgets

#### Scripts de Bouton

```lua
local button = CreateFrame("Button", "MyButton", UIParent)
button:SetSize(100, 32)
button:SetPoint("CENTER")

-- Textures
button:SetNormalTexture("Interface\\Buttons\\UI-Panel-Button-Up")
button:SetPushedTexture("Interface\\Buttons\\UI-Panel-Button-Down")
button:SetHighlightTexture("Interface\\Buttons\\UI-Panel-Button-Highlight")

-- Texte
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

-- Activer/Désactiver
button:Enable()
button:Disable()
```

#### EditBox (Champ de Texte)

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

### Chapitre 13: Répondre aux Événements

#### Gestionnaire d'Événements Propre

```lua
local addon = CreateFrame("Frame")
local events = {}

-- Fonction d'événement
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

-- Enregistrer tous les événements
for event in pairs(events) do
    addon:RegisterEvent(event)
end

-- Méthodes de l'addon
function addon:InitializeAddon()
    -- Initialisation
end

function addon:ScanBags()
    -- Scanner les sacs
end
```

#### SavedVariables

```lua
-- Dans le TOC
## SavedVariables: MyAddonDB
## SavedVariablesPerCharacter: MyAddonCharDB

-- Dans le code
local defaults = {
    enabled = true,
    scale = 1.0,
    position = {x = 0, y = 0}
}

function events:ADDON_LOADED(addonName)
    if addonName ~= "MyAddon" then return end

    -- Initialiser DB globale
    if not MyAddonDB then
        MyAddonDB = {}
    end

    -- Appliquer defaults
    for k, v in pairs(defaults) do
        if MyAddonDB[k] == nil then
            if type(v) == "table" then
                MyAddonDB[k] = CopyTable(v)
            else
                MyAddonDB[k] = v
            end
        end
    end

    -- DB par personnage
    if not MyAddonCharDB then
        MyAddonCharDB = {}
    end

    print("Addon loaded and configured!")
end
```

### Chapitre 14: Projet CombatTracker

#### Structure Complète d'un Addon

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

-- Fonctions
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

-- Événements
local events = {}

function events:PLAYER_REGEN_DISABLED()
    -- Combat démarre
    if not startTime then
        startTime = GetTime()
        damage = 0
    end
end

function events:PLAYER_REGEN_ENABLED()
    -- Combat termine
    UpdateText()
    -- Auto-reset après 5 secondes
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

-- Rendre draggable
addon:SetMovable(true)
addon:EnableMouse(true)
addon:RegisterForDrag("LeftButton")
addon:SetScript("OnDragStart", addon.StartMoving)
addon:SetScript("OnDragStop", addon.StopMovingOrSizing)

-- Clic droit pour rapporter
addon:SetScript("OnMouseUp", function(self, button)
    if button == "RightButton" then
        ReportDPS()
    end
end)
```

---

## Partie III: Techniques Avancées

### Chapitre 17: Slash Commands

#### Commande Simple

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

#### Commandes avec Arguments

```lua
local function HandleCommand(msg)
    -- Extraire commande et arguments
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

### Chapitre 18: OnUpdate

#### Throttling d'OnUpdate

```lua
local frame = CreateFrame("Frame")
local elapsed = 0
local updateInterval = 0.5  -- 2 fois par seconde

frame:SetScript("OnUpdate", function(self, delta)
    elapsed = elapsed + delta

    if elapsed >= updateInterval then
        -- Code exécuté toutes les 0.5 secondes
        self:UpdateData()
        elapsed = 0
    end
end)

function frame:UpdateData()
    -- Logique coûteuse
end
```

#### Timer Simple

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

-- Utilisation
CreateTimer(5, function()
    print("5 seconds elapsed!")
end)
```

### Chapitre 19: Function Hooking

#### Hook Basique

```lua
-- Sauvegarder la fonction originale
local original_GetItemInfo = GetItemInfo

-- Remplacer par la nouvelle fonction
GetItemInfo = function(...)
    -- Code avant
    print("GetItemInfo called")

    -- Appeler l'originale
    local name, link, quality, iLevel, reqLevel, class, subclass, maxStack,
          equipSlot, texture, vendorPrice = original_GetItemInfo(...)

    -- Code après
    if name then
        print("Item:", name)
    end

    -- Retourner les valeurs
    return name, link, quality, iLevel, reqLevel, class, subclass, maxStack,
           equipSlot, texture, vendorPrice
end
```

#### Hook Sécurisé

```lua
-- Hook pré (avant l'originale)
hooksecurefunc("UseAction", function(slot, checkCursor, onSelf)
    print("Action used:", slot)
end)

-- Hook de script de frame
local original_OnClick = MyButton:GetScript("OnClick")

MyButton:SetScript("OnClick", function(self, button, down)
    print("Button clicked!")

    if original_OnClick then
        original_OnClick(self, button, down)
    end
end)

-- Ou avec HookScript
MyButton:HookScript("OnClick", function(self, button, down)
    print("Hooked click!")
end)
```

### Chapitre 21: Combat Log

#### Parser le Combat Log

```lua
local frame = CreateFrame("Frame")
frame:RegisterEvent("COMBAT_LOG_EVENT_UNFILTERED")

local function ParseCombatLog(...)
    local timestamp, event, _, sourceGUID, sourceName, sourceFlags,
          destGUID, destName, destFlags = ...

    -- Ignorer si pas du joueur
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

## Exemples de Code Clés

### Exemple 1: Tracker de Cooldowns Complet

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

-- Titre
local title = CooldownTracker:CreateFontString(nil, "OVERLAY", "GameFontNormalLarge")
title:SetPoint("TOP", 0, -15)
title:SetText("Cooldowns")

-- Suivi des cooldowns
local cooldowns = {}
local bars = {}

-- Créer une barre de cooldown
local function CreateCooldownBar(spell, duration)
    local bar = CreateFrame("StatusBar", nil, CooldownTracker)
    bar:SetSize(180, 20)
    bar:SetStatusBarTexture("Interface\\TargetingFrame\\UI-StatusBar")
    bar:SetStatusBarColor(0.2, 0.6, 1.0)
    bar:SetMinMaxValues(0, duration)
    bar:SetValue(duration)

    -- Texte
    local text = bar:CreateFontString(nil, "OVERLAY", "GameFontNormalSmall")
    text:SetPoint("CENTER")
    text:SetText(spell)
    bar.text = text

    -- Position
    local yOffset = -40 - (table.getn(bars) * 25)
    bar:SetPoint("TOP", 0, yOffset)

    -- Données
    bar.spell = spell
    bar.startTime = GetTime()
    bar.duration = duration

    table.insert(bars, bar)
    return bar
end

-- Mise à jour des barres
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

    -- Réorganiser les barres actives
    for i, bar in ipairs(activeBars) do
        local yOffset = -40 - ((i-1) * 25)
        bar:SetPoint("TOP", 0, yOffset)
        bar:Show()
    end

    bars = activeBars
end

-- OnUpdate
CooldownTracker:SetScript("OnUpdate", UpdateBars)

-- Détecter les cooldowns
CooldownTracker:RegisterEvent("SPELL_UPDATE_COOLDOWN")
CooldownTracker:SetScript("OnEvent", function(self, event)
    local i = 1
    while true do
        local name, rank = GetSpellName(i, BOOKTYPE_SPELL)
        if not name then break end

        local start, duration = GetSpellCooldown(i, BOOKTYPE_SPELL)

        if duration > 1.5 then  -- Ignorer GCD
            local key = name .. (rank or "")
            if not cooldowns[key] then
                cooldowns[key] = true
                CreateCooldownBar(name, duration)

                -- Nettoyer après expiration
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

## Ressources et Outils

### Outils de Développement

1. **WowLua** : Console Lua in-game
2. **TexBrowser** : Navigateur de textures
3. **ArtBrowser** : http://wowprogramming.com
4. **DevTools** : Outils de debug Blizzard

### Sites Web Importants

- **WoWProgramming.com** : Documentation et tutoriels
- **WoWInterface.com** : Repository d'addons
- **WoWWiki** : Documentation API (archive)
- **GitHub** : Code source d'addons populaires

### Addons Populaires à Étudier

1. **TomTom** : Navigation
2. **Clique** : Click casting
3. **PerfectRaid** : Raid frames
4. **LightHeaded** : Base de données de quêtes
5. **Feed-O-Matic** : Pet feeding

### Commandes de Debug

```lua
-- Activer taint log
/console taintLog 1

-- Event trace
/eventtrace

-- Frame stack
/fstack

-- Dump frame info
/framestack

-- Recharger UI
/reload

-- Script errors
/console scriptErrors 1
```

### Snippets Utiles

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

-- Formatter l'argent
function FormatMoney(copper)
    local gold = floor(copper / 10000)
    local silver = floor((copper % 10000) / 100)
    local copper = copper % 100
    return string.format("%dg %ds %dc", gold, silver, copper)
end

-- Couleur de qualité
function GetQualityColor(quality)
    local color = ITEM_QUALITY_COLORS[quality]
    return color.r, color.g, color.b
end

-- Raccourci nombre
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

Ce livre est LA référence pour la programmation d'addons WoW. Il couvre :

✅ **Fondamentaux Lua** : Tout ce qu'il faut savoir
✅ **Interface WoW** : Frames, widgets, événements
✅ **Techniques avancées** : Combat log, secure templates, hooks
✅ **Projets pratiques** : BagBuddy, CombatTracker
✅ **Référence complète** : API, widgets, événements

**Pour aller plus loin** :
1. Lire le livre complet (62k lignes!)
2. Étudier le code FrameXML de Blizzard
3. Analyser des addons open-source
4. Pratiquer avec des petits projets
5. Partager et contribuer à la communauté

---

*Résumé basé sur "World of Warcraft Programming: A Guide and Reference for Creating WoW Addons, 2nd Edition"*
*Par James Whitehead II (Cladhaire) et Rick Roe (Gazmik Fizzwidget)*
*Publié par Wiley Publishing, Inc. - 2010*
