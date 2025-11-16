# WoW Addon Development Reference Guide (Vanilla 1.12.1)

This document compiles the WoW API, events and global functions to facilitate addon development for World of Warcraft version 1.12.1 (Vanilla).

---

**⚠️ IMPORTANT - Lua 5.0 (WoW Vanilla 1.12)**

This guide is for **WoW Vanilla 1.12** which uses **Lua 5.0**, not Lua 5.1+.

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

1. [Introduction](#introduction)
2. [Notations and Conventions](#notations-and-conventions)
3. [Global Functions API](#global-functions-api)
4. [Event System](#event-system)
5. [Frame Management](#frame-management)
6. [Best Practices](#best-practices)
7. [Code Examples](#code-examples)

---

## Introduction

This guide serves as a quick reference for WoW Vanilla addon development. It covers:
- Available API functions
- Game events
- Frame and UI systems
- Development best practices

### Source Resources
- `apiwow`: Detailed API functions by category
- `global-api-wow.txt`: Complete list of global functions
- `wow-events.txt`: Complete catalog of WoW events

---

## Notations and Conventions

### Function Prefixes

- **UI**: Function implemented in Lua in Blizzard's user interface (FrameXML)
- **PROTECTED**: Function accessible only to Blizzard-signed addons
- **REMOVED**: Function no longer available

### Common Return Types

- `nil`: Null value or no result
- `1` / `nil`: Boolean (1 = true, nil = false)
- `"string"`: String
- `number`: Numeric value

---

## Global Functions API

### 1. Action Functions (Action Buttons)

These functions manage action bars and their buttons.

```lua
-- Action button management
ActionButtonDown(id)                    -- Press an action button
ActionButtonUp(id)                      -- Release an action button
HasAction(slot)                         -- Check if action exists (returns 1 or nil)
UseAction(slot[, checkCursor[, onSelf]]) -- Use action in slot

-- Action information
GetActionCooldown(slot)                 -- Returns start, duration, enable
GetActionCount(slot)                    -- Number of uses (potions, etc.)
GetActionText(slot)                     -- Action text (macros)
GetActionTexture(slot)                  -- Texture path

-- State tests
IsActionInRange(slot)                   -- 1=yes, 0=no, nil=N/A
IsAttackAction(slot)                    -- 1 if attack action
IsAutoRepeatAction(slot)                -- 1 if auto-repeat
IsCurrentAction(slot)                   -- 1 if current action
IsUsableAction(slot)                    -- 1 if usable
IsConsumableAction(slot)                -- 1 if consumable
IsEquippedAction(slot)                  -- 1 if requires equipment

-- Action manipulation
PickupAction(slot)                      -- Pick up action to cursor
PlaceAction(slot)                       -- Place action from cursor
```

### 2. Player Activity Functions

```lua
-- Combat and Duel
AttackTarget()                          -- Attack target
AcceptDuel()                            -- Accept duel
CancelDuel()                            -- Decline duel
StartDuel("name")                       -- Challenge player
StartDuelUnit("unit")                   -- Challenge unit

-- General actions
SitOrStand()                            -- Sit or stand
TogglePVP()                             -- Toggle PVP
ToggleSheath()                          -- Sheathe/unsheathe weapons
RandomRoll(low, high)                   -- Roll dice

-- Logout
Logout()                                -- Logout
Quit()                                  -- Quit game
CancelLogout()                          -- Cancel logout
```

### 3. AddOn Management

```lua
-- Load/Unload addons
LoadAddOn(index or "name")              -- Load LoD addon
IsAddOnLoaded(index or "name")          -- Check if loaded
IsAddOnLoadOnDemand(index or "name")    -- Check if LoD

-- Addon information
GetNumAddOns()                          -- Number of addons
GetAddOnInfo(index or "name")           -- Addon info
GetAddOnMetadata(index, "variable")     -- Read TOC metadata
GetAddOnDependencies(index or "name")   -- List dependencies

-- Enable/Disable
EnableAddOn(index or "name")            -- Enable for future sessions
DisableAddOn(index or "name")           -- Disable for future sessions
EnableAllAddOns()                       -- Enable all
DisableAllAddOns()                      -- Disable all
```

### 4. Communication Functions

```lua
-- Chat messages
SendChatMessage("text"[, "type"[, language[, target]]])
-- Types: "SAY", "YELL", "PARTY", "RAID", "GUILD", "OFFICER", "WHISPER", "CHANNEL"

-- Chat channels
JoinChannelByName("channel"[, "password"[, frameId]])
LeaveChannelByName("channel")
ListChannels()                          -- List all channels
GetChannelList()                        -- Joined channels
GetChannelName("channel" or index)      -- Channel name

-- Addon communication (v1.12+)
SendAddonMessage("prefix", "text", "type")

-- Emotes
DoEmote("emote"[, "target"])           -- Execute emote

-- Languages
GetDefaultLanguage("unit")              -- Default language
GetLanguageByIndex(index)               -- Language by index
GetNumLaguages()                        -- Number of languages
```

### 5. Container Management (Bags)

```lua
-- Bag information
GetBagName(bagID)                       -- Bag name
GetContainerNumSlots(bagID)             -- Number of slots
GetContainerItemInfo(bagID, slot)       -- Item info
GetContainerItemLink(bagID, slot)       -- ItemLink
GetContainerItemCooldown(bagID, slot)   -- Item cooldown

-- Item usage
UseContainerItem(bagID, slot[, onSelf]) -- Use item
PickupContainerItem(bagID, slot)        -- Pick up to cursor
SplitContainerItem(bagID, slot, amount) -- Split stack

-- Interface
ToggleBackpack()                        -- Open/close backpack
ToggleBag(bagID)                        -- Open/close bag
OpenAllBags()                           -- Open/close all bags

-- Item placement
PutItemInBackpack()                     -- Place in backpack
PutItemInBag(inventoryId)               -- Place in specific bag
```

### 6. Character Functions

```lua
-- Statistics
GetMoney()                              -- Money in copper
GetComboPoints()                        -- Combo points
GetBlockChance()                        -- % block chance
GetDodgeChance()                        -- % dodge chance
GetParryChance()                        -- % parry chance

-- Rest state
IsResting()                             -- Resting?
GetRestState()                          -- Rest state
GetXPExhaustion()                       -- Remaining rest XP

-- Resurrection
AcceptResurrect()                       -- Accept resurrection
DeclineResurrect()                      -- Decline resurrection
RetrieveCorpse()                        -- Retrieve corpse
ResurrectHasSickness()                  -- Has resurrection sickness?
UseSoulstone()                          -- Use soulstone

-- Hearthstone
GetBindLocation()                       -- Hearthstone location
ConfirmBinder()                         -- Confirm new location
CheckBinderDist()                       -- Close enough?
```

### 7. Group and Raid Functions

```lua
-- Group management
AcceptGroup()                           -- Accept invitation
DeclineGroup()                          -- Decline invitation
InviteByName("name")                    -- Invite player
InviteToParty("unit")                   -- Invite unit
LeaveParty()                            -- Leave group
UninviteByName("name")                  -- Kick player

-- Group information
GetNumPartyMembers()                    -- Number of members
GetPartyMember(index)                   -- Member UnitID
IsPartyLeader()                         -- Is leader?
GetPartyLeaderIndex()                   -- Leader index
UnitInParty("unit")                     -- Unit in party?

-- Leadership
PromoteByName("name")                   -- Promote
PromoteToPartyLeader("unit")            -- Promote to leader
ConvertToRaid()                         -- Convert to raid

-- Loot
GetLootMethod()                         -- Loot method
SetLootMethod("method"[, "master" or threshold])
GetLootThreshold()                      -- Loot threshold
SetLootThreshold(itemQuality)           -- Set threshold
GetMasterLootCandidate(index)           -- ML candidate

-- Ready check
DoReadyCheck()                          -- Start ready check
ConfirmReadyCheck(isReady)              -- Respond to ready check
```

### 8. Unit Functions (UnitID)

UnitIDs are identifiers like: `"player"`, `"target"`, `"party1"` to `"party4"`, `"raid1"` to `"raid40"`, `"pet"`, `"mouseover"`, etc.

```lua
-- Basic information
UnitName("unit")                        -- Unit name
UnitLevel("unit")                       -- Level
UnitClass("unit")                       -- Class
UnitRace("unit")                        -- Race
UnitSex("unit")                         -- Sex (1=unknown, 2=M, 3=F)
UnitFactionGroup("unit")                -- Faction ("Alliance"/"Horde")

-- Health state
UnitHealth("unit")                      -- Current health
UnitHealthMax("unit")                   -- Max health
UnitMana("unit")                        -- Mana/Energy/Rage
UnitManaMax("unit")                     -- Maximum
UnitIsDead("unit")                      -- Is dead?
UnitIsGhost("unit")                     -- Is ghost?

-- State
UnitIsPlayer("unit")                    -- Is player?
UnitIsUnit("unit1", "unit2")            -- unit1 == unit2?
UnitInParty("unit")                     -- In party?
UnitInRaid("unit")                      -- In raid?
UnitIsConnected("unit")                 -- Is connected?
UnitAffectingCombat("unit")             -- In combat?

-- Buffs and Debuffs
UnitBuff("unit", index[, showCastable]) -- Buff info
UnitDebuff("unit", index[, showDispellable]) -- Debuff info

-- Targeting
UnitCanAttack("player", "unit")         -- Can attack?
UnitCanAssist("player", "unit")         -- Can assist?
UnitIsEnemy("player", "unit")           -- Is enemy?
UnitIsFriend("player", "unit")          -- Is friend?
```

### 9. Spell Functions

```lua
-- Spellbook
GetNumSpellTabs()                       -- Number of tabs
GetSpellTabInfo(index)                  -- Tab info
GetSpellName(spellID, "bookType")       -- Spell name
GetSpellTexture(spellID, "bookType")    -- Spell texture
GetSpellCooldown(spellID, "bookType")   -- Cooldown

-- Cast spells
CastSpell(spellID, "bookType")          -- Cast spell
CastSpellByName("name"[, onSelf])       -- Cast by name
SpellStopCasting()                      -- Cancel cast

-- Manipulation
PickupSpell(spellID, "bookType")        -- Pick up to cursor

-- Note: bookType can be "spell" or "pet"
```

### 10. Quest Functions

```lua
-- Quest list
GetNumQuestLogEntries()                 -- Number of quests
GetQuestLogTitle(index)                 -- Quest title
SelectQuestLogEntry(index)              -- Select quest
GetQuestLogSelection()                  -- Selected quest

-- Quest details
GetNumQuestLeaderBoards(index)          -- Number of objectives
GetQuestLogLeaderBoard(index)           -- Objective details

-- NPC interactions
GetNumQuestChoices()                    -- Number of choice rewards
GetNumQuestRewards()                    -- Number of fixed rewards
GetQuestReward(index)                   -- Choose reward
```

### 11. Inventory Functions

```lua
-- Equipment slots
GetInventorySlotInfo("slotName")        -- Slot ID
GetInventoryItemLink("unit", slotID)    -- Equipped ItemLink
GetInventoryItemTexture("unit", slotID) -- Texture
GetInventoryItemCount("unit", slotID)   -- Count (ammo)
GetInventoryItemCooldown("unit", slotID) -- Cooldown

-- Manipulation
PickupInventoryItem(invSlot)            -- Pick up to cursor
EquipCursorItem(invSlot)                -- Equip from cursor
AutoEquipCursorItem()                   -- Auto-equip

-- Slots: "HeadSlot", "NeckSlot", "ShoulderSlot", "BackSlot", "ChestSlot",
-- "ShirtSlot", "TabardSlot", "WristSlot", "HandsSlot", "WaistSlot",
-- "LegsSlot", "FeetSlot", "Finger0Slot", "Finger1Slot", "Trinket0Slot",
-- "Trinket1Slot", "MainHandSlot", "SecondaryHandSlot", "RangedSlot", "AmmoSlot"
```

### 12. Targeting Functions

```lua
-- Basic targeting
TargetUnit("unit")                      -- Target unit
TargetByName("name"[, exactMatch])      -- Target by name
ClearTarget()                           -- Clear target
AssistUnit("unit")                      -- Assist unit

-- Contextual targeting
TargetNearestEnemy([reverseFlag])       -- Nearest enemy
TargetNearestFriend([reverseFlag])      -- Nearest friend
TargetLastTarget()                      -- Last target
TargetLastEnemy()                       -- Last enemy

-- Targeting by type
TargetNearestPartyMember()              -- Nearest party member
TargetNearestRaidMember()               -- Nearest raid member
```

### 13. Camera Functions

```lua
-- Zoom
CameraZoomIn(increment)                 -- Zoom in
CameraZoomOut(increment)                -- Zoom out

-- Rotation
MoveViewLeftStart()                     -- Rotate left (start)
MoveViewLeftStop()                      -- Rotate left (stop)
MoveViewRightStart()                    -- Rotate right (start)
MoveViewRightStop()                     -- Rotate right (stop)
MoveViewUpStart()                       -- Rotate up (start)
MoveViewUpStop()                        -- Rotate up (stop)
MoveViewDownStart()                     -- Rotate down (start)
MoveViewDownStop()                      -- Rotate down (stop)

-- Predefined views
SetView(index)                          -- View 1-5
SaveView(index)                         -- Save view
ResetView(index)                        -- Reset view
NextView()                              -- Next view
PrevView()                              -- Previous view

-- MouseLook
IsMouselooking()                        -- In mouselook?
MouselookStart()                        -- Start mouselook
MouselookStop()                         -- Stop mouselook
```

### 14. Time and Debug Functions

```lua
-- Time
GetTime()                               -- Time in seconds since startup
time()                                  -- UNIX timestamp
date(format[, time])                    -- Format date

-- Debug
debugprofilestart()                     -- Start profiling timer
debugprofilestop()                      -- Elapsed time in ms
debugstack([start[, count1, count2]])   -- Stack trace
DEFAULT_CHAT_FRAME:AddMessage("text")   -- Display in chat
```

### 15. Cursor Functions

```lua
-- Cursor state
CursorHasItem()                         -- Has item?
CursorHasMoney()                        -- Has money?
CursorHasSpell()                        -- Has spell?
GetCursorPosition()                     -- Position X, Y

-- Manipulation
ClearCursor()                           -- Clear cursor
ResetCursor()                           -- Reset
DeleteCursorItem()                      -- Destroy item
DropCursorMoney()                       -- Drop money

-- Money
GetCursorMoney()                        -- Amount on cursor
PickupPlayerMoney(amount)               -- Pick up money
DropCursorMoney()                       -- Drop money
```

---

## Event System

### Event Registration and Management

```lua
-- In a frame
frame:RegisterEvent("EVENT_NAME")       -- Register event
frame:UnregisterEvent("EVENT_NAME")     -- Unregister
frame:UnregisterAllEvents()             -- Unregister all
frame:IsEventRegistered("EVENT_NAME")   -- Check registration

-- Event handler
frame:SetScript("OnEvent", function(self, event, ...)
    if event == "EVENT_NAME" then
        local arg1, arg2 = ...
        -- Process event
    end
end)
```

### Main Events by Category

#### 1. Login/Logout Events

```
PLAYER_LOGIN
    - Triggered on login, before PLAYER_ENTERING_WORLD
    - First useful event for initialization

PLAYER_ENTERING_WORLD
    - Triggered when player enters world
    - Also triggered during teleports/instances/reloads

PLAYER_LEAVING_WORLD
    - Triggered on logout

PLAYER_LOGOUT
    - After PLAYER_LEAVING_WORLD, before variable save

VARIABLES_LOADED
    - SavedVariables loaded in memory

ADDON_LOADED
    - arg1: loaded addon name
    - Triggered for each addon
```

#### 2. Combat Events

```
PLAYER_ENTER_COMBAT
    - Triggered at auto-attack start
    - Does NOT include spells or aggro

PLAYER_LEAVE_COMBAT
    - Triggered at auto-attack end

PLAYER_REGEN_DISABLED
    - Triggered when receiving aggro
    - More reliable for combat detection

PLAYER_REGEN_ENABLED
    - Triggered a few seconds after combat end
    - Normal regeneration resumes

PLAYER_DEAD
    - Player is dead

PLAYER_ALIVE
    - Player is alive (after release, feign death, rez before release)

PLAYER_UNGHOST
    - Player resurrected after release
```

#### 3. Chat Events

```
CHAT_MSG_SAY
    - arg1: message, arg2: author, arg3: language

CHAT_MSG_YELL
    - arg1: message, arg2: author, arg3: language

CHAT_MSG_PARTY
    - arg1: message, arg2: author, arg3: language

CHAT_MSG_RAID
    - Raid message

CHAT_MSG_GUILD
    - arg1: message, arg2: author, arg3: language

CHAT_MSG_OFFICER
    - arg1: message, arg2: author, arg3: language

CHAT_MSG_WHISPER
    - arg1: message, arg2: author, arg6: status (AFK/DND)

CHAT_MSG_WHISPER_INFORM
    - Whisper sent
    - arg1: message, arg2: recipient

CHAT_MSG_CHANNEL
    - arg1: message, arg2: author, arg4: channel with number
    - arg8: channel number, arg9: channel name without number

CHAT_MSG_ADDON
    - Inter-addon message (v1.12+)

CHAT_MSG_SYSTEM
    - System messages (yellow)
    - arg1: message content
```

#### 4. Unit Events

```
UNIT_HEALTH
    - arg1: unitID
    - Unit health changes

UNIT_MANA / UNIT_ENERGY / UNIT_RAGE
    - arg1: unitID
    - Resource changes

UNIT_MAXHEALTH / UNIT_MAXMANA
    - Maximum changes

UNIT_AURA
    - arg1: unitID
    - Buff/debuff gained or lost
    - Also triggered on target change

UNIT_COMBAT
    - arg1: unitID, arg2: action, arg3: critical, arg4: damage, arg5: type

UNIT_LEVEL
    - arg1: unitID
    - Level changed/submitted

UNIT_NAME_UPDATE
    - arg1: unitID
    - Name changes

PLAYER_TARGET_CHANGED
    - Player target changes

PLAYER_PET_CHANGED
    - Pet changes
```

#### 5. Group/Raid Events

```
PARTY_MEMBERS_CHANGED
    - Party composition changes
    - Triggered multiple times on invitation

PARTY_LEADER_CHANGED
    - Party leader changes

PARTY_LOOT_METHOD_CHANGED
    - Loot method changes

RAID_ROSTER_UPDATE
    - Raid composition changes
    - Also if loot rules change

PARTY_MEMBER_ENABLE
    - arg1: player name
    - Member connected/alive

PARTY_MEMBER_DISABLE
    - arg1: player name
    - Member disconnected/dead
```

#### 6. Inventory and Bag Events

```
BAG_UPDATE
    - arg1: bagID
    - Bag content changes

BAG_UPDATE_COOLDOWN
    - arg1: bagID
    - Cooldown in bag

ITEM_LOCK_CHANGED
    - Item locked/unlocked

UNIT_INVENTORY_CHANGED
    - arg1: unitID
    - Equipment changes

PLAYER_MONEY
    - Money changes

ITEM_PUSH
    - arg1: stack size, arg2: icon
    - Item created/looted
```

#### 7. Spell Events

```
SPELLCAST_START
    - arg1: spell name, arg2: duration in ms
    - Cast starts

SPELLCAST_STOP
    - Cast ends or cancels

SPELLCAST_FAILED
    - Cast fails

SPELLCAST_INTERRUPTED
    - Cast interrupted

SPELLCAST_DELAYED
    - arg1: delay
    - Cast delayed

SPELLCAST_CHANNEL_START
    - arg1: duration in ms, arg2: spell name
    - Channeling starts

SPELLCAST_CHANNEL_UPDATE
    - arg1: remaining duration in ms
    - Channeling ongoing

SPELLCAST_CHANNEL_STOP
    - Channeling ends

SPELL_UPDATE_COOLDOWN
    - Spell cooldown starts

LEARNED_SPELL_IN_TAB
    - arg1: tab number
    - New spell learned
```

#### 8. Interface Events

```
ACTIONBAR_SLOT_CHANGED
    - arg1: slot number
    - Action bar content changes

ACTIONBAR_PAGE_CHANGED
    - Action bar page changes

ACTIONBAR_UPDATE_COOLDOWN
    - Cooldown on action bar

UPDATE_BINDINGS
    - Key bindings change

UPDATE_MACROS
    - Macros change

PLAYER_AURAS_CHANGED
    - Player auras change
    - Also for druid form changes

PLAYER_COMBO_POINTS
    - Combo points change
    - Use GetComboPoints() for count
```

#### 9. Quest Events

```
QUEST_LOG_UPDATE
    - Quest log changes
    - Very frequent

QUEST_DETAIL
    - Quest details displayed

QUEST_PROGRESS
    - Quest progress displayed

QUEST_COMPLETE
    - Quest complete, before turn-in

QUEST_FINISHED
    - Quest window changes or closes

QUEST_GREETING
    - Quest offered
```

#### 10. Mail Events

```
MAIL_SHOW
    - Mailbox opened

MAIL_CLOSED
    - Mailbox closed

MAIL_INBOX_UPDATE
    - Mailbox content changes

MAIL_SEND_SUCCESS
    - Mail sent successfully

UPDATE_PENDING_MAIL
    - New mail notification
```

#### 11. Merchant/Vendor Events

```
MERCHANT_SHOW
    - Merchant window opened

MERCHANT_CLOSED
    - Merchant window closed

MERCHANT_UPDATE
    - Merchant inventory changes
```

#### 12. Zone Events

```
ZONE_CHANGED
    - Zone changes (minimap text)

ZONE_CHANGED_INDOORS
    - Indoor zone changes

ZONE_CHANGED_NEW_AREA
    - New region (new channels)

MINIMAP_ZONE_CHANGED
    - Minimap zone changes
```

#### 13. Loot Events

```
LOOT_OPENED
    - Loot window opened

LOOT_CLOSED
    - Loot window closed

LOOT_SLOT_CLEARED
    - arg1: slot
    - Loot item removed

START_LOOT_ROLL
    - arg1: rollID, arg2: time
    - Loot rolling
```

#### 14. PvP/Battleground Events

```
UPDATE_BATTLEFIELD_STATUS
    - Battlefield status changes

UPDATE_BATTLEFIELD_SCORE
    - Scores change

PLAYER_PVP_KILLS_CHANGED
    - PvP kill count changes

PLAYER_FLAGS_CHANGED
    - arg1: unitID
    - Flags change (AFK, DND, etc.)
```

---

## Frame Management

### Creating Frames

```lua
-- Via XML (recommended for complex UI)
<Frame name="MyFrame" parent="UIParent">
    <Size>
        <AbsDimension x="200" y="100"/>
    </Size>
    <Anchors>
        <Anchor point="CENTER"/>
    </Anchors>
    <Scripts>
        <OnLoad>
            self:RegisterEvent("PLAYER_LOGIN");
        </OnLoad>
        <OnEvent>
            if event == "PLAYER_LOGIN" then
                DEFAULT_CHAT_FRAME:AddMessage("Addon loaded!");
            end
        </OnEvent>
    </Scripts>
</Frame>

-- Via Lua (for dynamic frames)
local frame = CreateFrame("Frame", "MyLuaFrame", UIParent)
frame:SetWidth(200)
frame:SetHeight(100)
frame:SetPoint("CENTER")

-- Event registration
frame:RegisterEvent("PLAYER_LOGIN")
frame:SetScript("OnEvent", function(self, event, ...)
    if event == "PLAYER_LOGIN" then
        DEFAULT_CHAT_FRAME:AddMessage("Frame created dynamically!")
    end
end)
```

### Frame Types

```
Frame           - Basic frame, container
Button          - Clickable button
CheckButton     - Checkbox
EditBox         - Text input field
ScrollFrame     - Scrollable frame
Slider          - Value slider
StatusBar       - Progress bar
Texture         - Image/texture
FontString      - Text
```

### Frame Scripts

```lua
frame:SetScript("OnLoad", function(self)
    -- Initialization
end)

frame:SetScript("OnShow", function(self)
    -- When shown
end)

frame:SetScript("OnHide", function(self)
    -- When hidden
end)

frame:SetScript("OnUpdate", function(self, elapsed)
    -- Every frame (~60 fps)
    -- elapsed = time since last update
end)

frame:SetScript("OnEvent", function(self, event, ...)
    -- Event handler
end)

-- For buttons
button:SetScript("OnClick", function(self, button)
    -- button = "LeftButton", "RightButton", etc.
end)

button:SetScript("OnEnter", function(self)
    -- Mouse enters button
end)

button:SetScript("OnLeave", function(self)
    -- Mouse leaves button
end)
```

### Positioning (Anchors)

```lua
frame:SetPoint("point"[, relativeTo[, "relativePoint"[, xOffset, yOffset]]])

-- Points: "TOPLEFT", "TOP", "TOPRIGHT", "LEFT", "CENTER", "RIGHT",
--         "BOTTOMLEFT", "BOTTOM", "BOTTOMRIGHT"

-- Examples
frame:SetPoint("CENTER")                        -- Screen center
frame:SetPoint("TOPLEFT", 10, -10)              -- Top left + offset
frame:SetPoint("BOTTOM", UIParent, "BOTTOM", 0, 50) -- Bottom screen + 50px
frame:SetPoint("LEFT", otherFrame, "RIGHT", 5, 0)   -- To right of frame
```

### Textures and FontStrings

```lua
-- Texture
local texture = frame:CreateTexture(nil, "BACKGROUND")
texture:SetTexture("Interface\\Icons\\INV_Misc_QuestionMark")
texture:SetAllPoints(frame)  -- Fill entire frame

-- FontString
local text = frame:CreateFontString(nil, "OVERLAY", "GameFontNormal")
text:SetPoint("CENTER")
text:SetText("My text")
text:SetTextColor(1, 1, 1)  -- RGB (0-1)
```

---

## Best Practices

### 1. Addon Structure

```
MyAddon/
├── MyAddon.toc        # Configuration file
├── MyAddon.lua        # Main code
├── MyAddon.xml        # Interface (optional)
├── Localization.lua   # Translations (optional)
└── README.txt         # Documentation
```

### 2. TOC File

```toc
## Interface: 11200
## Title: My Super Addon
## Notes: Description of my addon
## Author: YourName
## Version: 1.0.0
## SavedVariables: MyAddonDB
## SavedVariablesPerCharacter: MyAddonCharDB

# Files to load (order important!)
Localization.lua
MyAddon.xml
MyAddon.lua
```

### 3. Clean Initialization

```lua
-- Create namespace to avoid global conflicts
-- Note Lua 5.0: no automatic ... in .lua files
MyAddon = MyAddon or {}

-- Main frame
local frame = CreateFrame("Frame")

-- Local variables for performance
local DEFAULT_CHAT_FRAME = DEFAULT_CHAT_FRAME
local UnitName = UnitName
local GetTime = GetTime

-- Event management table
local events = {}

function events:PLAYER_LOGIN()
    -- Login initialization
    if not MyAddonDB then
        MyAddonDB = {
            enabled = true,
            options = {}
        }
    end
    DEFAULT_CHAT_FRAME:AddMessage("My Addon loaded!")
end

function events:PLAYER_ENTERING_WORLD()
    -- Actions on entering world
end

-- Event dispatcher (Lua 5.0)
frame:SetScript("OnEvent", function()
    -- In Lua 5.0: event, arg1, arg2, etc. are global in handler
    -- 'this' refers to the frame
    if events[event] then
        -- Pass current arguments
        events[event](this, arg1, arg2, arg3, arg4, arg5)
    end
end)

-- Event registration
for event, _ in pairs(events) do
    frame:RegisterEvent(event)
end
```

### 4. SavedVariables Management

```lua
-- Default values
local defaults = {
    enabled = true,
    scale = 1.0,
    position = {
        point = "CENTER",
        x = 0,
        y = 0
    }
}

-- Initialization with merge
function InitDB()
    MyAddonDB = MyAddonDB or {}

    -- Copy missing defaults
    for k, v in pairs(defaults) do
        if MyAddonDB[k] == nil then
            if type(v) == "table" then
                MyAddonDB[k] = {}
                for k2, v2 in pairs(v) do
                    MyAddonDB[k][k2] = v2
                end
            else
                MyAddonDB[k] = v
            end
        end
    end
end
```

### 5. Performance and Optimization

```lua
-- Cache frequently used functions
local gsub = string.gsub
local format = string.format
local floor = math.floor
local pairs = pairs
local ipairs = ipairs

-- Avoid repeated creations
local frame = CreateFrame("Frame")  -- ONCE
-- NOT in OnUpdate or frequent events!

-- Throttling for OnUpdate
local updateInterval = 0.1  -- 10 times per second
local timeSinceLastUpdate = 0

frame:SetScript("OnUpdate", function(self, elapsed)
    timeSinceLastUpdate = timeSinceLastUpdate + elapsed

    if timeSinceLastUpdate >= updateInterval then
        -- Code executed every 0.1 seconds
        timeSinceLastUpdate = 0
    end
end)

-- Unregister unused events
frame:UnregisterEvent("UNUSED_EVENT")
```

### 6. Error Handling

```lua
-- Protected call to avoid breaking errors
local success, result = pcall(function()
    -- Code that might fail
    return DoSomethingRisky()
end)

if not success then
    DEFAULT_CHAT_FRAME:AddMessage("Error: " .. tostring(result))
end

-- Parameter validation
function MyAddon:DoSomething(unit)
    if not unit or not UnitExists(unit) then
        return false, "Invalid unit"
    end

    -- Rest of code
    return true
end
```

### 7. Slash Commands

```lua
SLASH_MYADDON1 = "/myaddon"
SLASH_MYADDON2 = "/ma"

SlashCmdList["MYADDON"] = function(msg)
    msg = msg:lower():trim()

    if msg == "show" then
        MyAddonFrame:Show()
    elseif msg == "hide" then
        MyAddonFrame:Hide()
    elseif msg == "toggle" then
        if MyAddonFrame:IsShown() then
            MyAddonFrame:Hide()
        else
            MyAddonFrame:Show()
        end
    else
        DEFAULT_CHAT_FRAME:AddMessage("Available commands:")
        DEFAULT_CHAT_FRAME:AddMessage("/myaddon show - Show")
        DEFAULT_CHAT_FRAME:AddMessage("/myaddon hide - Hide")
        DEFAULT_CHAT_FRAME:AddMessage("/myaddon toggle - Toggle")
    end
end
```

---

## Code Examples

### Example 1: Cooldown Tracker

```lua
local frame = CreateFrame("Frame")
local cooldowns = {}

-- Monitor cooldowns
frame:RegisterEvent("SPELL_UPDATE_COOLDOWN")
frame:SetScript("OnEvent", function(self, event)
    -- Scan all spells
    local i = 1
    while true do
        local name, rank = GetSpellName(i, BOOKTYPE_SPELL)
        if not name then break end

        local start, duration = GetSpellCooldown(i, BOOKTYPE_SPELL)

        if duration > 1.5 then  -- Ignore GCD
            local key = name .. (rank or "")
            if not cooldowns[key] then
                cooldowns[key] = {
                    start = start,
                    duration = duration,
                    name = name,
                    rank = rank
                }
                DEFAULT_CHAT_FRAME:AddMessage(
                    name .. " on cooldown for " .. duration .. "s"
                )
            end
        end

        i = i + 1
    end
end)

-- Clean up expired cooldowns
frame:SetScript("OnUpdate", function(self, elapsed)
    local now = GetTime()
    for k, cd in pairs(cooldowns) do
        if now >= cd.start + cd.duration then
            cooldowns[k] = nil
        end
    end
end)
```

### Example 2: Raid Boss Detection

```lua
local frame = CreateFrame("Frame")
local bossNames = {
    ["Ragnaros"] = true,
    ["Onyxia"] = true,
    ["Nefarian"] = true,
    -- Add other bosses
}

frame:RegisterEvent("PLAYER_TARGET_CHANGED")
frame:SetScript("OnEvent", function(self, event)
    if UnitExists("target") then
        local name = UnitName("target")
        local level = UnitLevel("target")

        -- Boss = level -1 or in list
        if level == -1 or bossNames[name] then
            DEFAULT_CHAT_FRAME:AddMessage(
                "BOSS DETECTED: " .. name,
                1, 0, 0  -- Red
            )

            -- Additional actions
            PlaySound("RaidWarning")
        end
    end
end)
```

### Example 3: Auto-Repair

```lua
local frame = CreateFrame("Frame")

frame:RegisterEvent("MERCHANT_SHOW")
frame:SetScript("OnEvent", function(self, event)
    if CanMerchantRepair() then
        local cost = GetRepairAllCost()

        if cost > 0 then
            if GetMoney() >= cost then
                RepairAllItems()

                local gold = floor(cost / 10000)
                local silver = floor((cost % 10000) / 100)
                local copper = cost % 100

                DEFAULT_CHAT_FRAME:AddMessage(
                    string.format(
                        "Items repaired for %dg %ds %dc",
                        gold, silver, copper
                    ),
                    1, 1, 0
                )
            else
                DEFAULT_CHAT_FRAME:AddMessage(
                    "Not enough money to repair!",
                    1, 0, 0
                )
            end
        end
    end
end)
```

### Example 4: Party Buff Tracker

```lua
local frame = CreateFrame("Frame")
local buffToTrack = "Power Word: Fortitude"  -- Example

local function CheckUnitBuff(unit)
    local i = 1
    while UnitBuff(unit, i) do
        local buffName = UnitBuff(unit, i)
        if buffName == buffToTrack then
            return true
        end
        i = i + 1
    end
    return false
end

local function ScanPartyBuffs()
    local missing = {}

    -- Check player
    if not CheckUnitBuff("player") then
        table.insert(missing, UnitName("player"))
    end

    -- Check party
    for i = 1, GetNumPartyMembers() do
        local unit = "party" .. i
        if not CheckUnitBuff(unit) then
            table.insert(missing, UnitName(unit))
        end
    end

    if table.getn(missing) > 0 then
        DEFAULT_CHAT_FRAME:AddMessage(
            "Missing " .. buffToTrack .. ": " .. table.concat(missing, ", ")
        )
    end
end

-- Check when composition changes
frame:RegisterEvent("PARTY_MEMBERS_CHANGED")
frame:RegisterEvent("UNIT_AURA")
frame:SetScript("OnEvent", function(self, event, arg1)
    if event == "PARTY_MEMBERS_CHANGED" or
       (event == "UNIT_AURA" and (arg1 == "player" or arg1:find("party"))) then
        ScanPartyBuffs()
    end
end)
```

### Example 5: Simple DPS Calculator

```lua
local frame = CreateFrame("Frame")
local damage = 0
local combatStart = nil

frame:RegisterEvent("CHAT_MSG_COMBAT_SELF_HITS")
frame:RegisterEvent("PLAYER_REGEN_DISABLED")
frame:RegisterEvent("PLAYER_REGEN_ENABLED")

frame:SetScript("OnEvent", function(self, event, arg1)
    if event == "PLAYER_REGEN_DISABLED" then
        -- Combat starts
        damage = 0
        combatStart = GetTime()

    elseif event == "PLAYER_REGEN_ENABLED" then
        -- Combat ends
        if combatStart then
            local duration = GetTime() - combatStart
            local dps = damage / duration

            DEFAULT_CHAT_FRAME:AddMessage(
                string.format(
                    "Combat: %.1fs | Damage: %d | DPS: %.1f",
                    duration, damage, dps
                ),
                1, 1, 0
            )

            combatStart = nil
        end

    elseif event == "CHAT_MSG_COMBAT_SELF_HITS" then
        -- Parse damage from message
        -- Format: "Your Attack hits Enemy for X."
        local dmg = arg1:match("for (%d+)")
        if dmg then
            damage = damage + tonumber(dmg)
        end
    end
end)
```

---

## Resources and References

### Useful Undocumented Functions

```lua
-- String helpers
string.trim(s)                          -- Remove leading/trailing spaces
string.split(sep, str)                  -- Split string (v1.11+)

-- Table helpers
table.getn(t)                           -- Table size (#t in Lua 5.1+)

-- UI helpers
UIParent                                -- UI parent frame
WorldFrame                              -- 3D world frame

-- Item quality colors
ITEM_QUALITY_COLORS[quality]            -- quality = 0-7
```

### Item Quality Codes

```
0 = Poor (Gray)
1 = Common (White)
2 = Uncommon (Green)
3 = Rare (Blue)
4 = Epic (Purple)
5 = Legendary (Orange)
6 = Artifact (Yellow/Gold)
7 = Heirloom (Blizzard only)
```

### Container IDs

```
0  = Backpack
1  = First bag
2  = Second bag
3  = Third bag
4  = Fourth bag
-1 = Bank
5  = First bank bag
6  = Second bank bag
...
11 = Seventh bank bag
```

### Class Codes

```lua
local _, englishClass = UnitClass("player")
-- englishClass can be:
-- "WARRIOR", "PALADIN", "HUNTER", "ROGUE", "PRIEST",
-- "SHAMAN", "MAGE", "WARLOCK", "DRUID"
```

---

## Conclusion

This document is a starting point for WoW Vanilla addon development. For more detailed information:

1. Examine existing quality addons (like CTMod, Cosmos, etc.)
2. Consult Blizzard's FrameXML files in the installation folder
3. Test your addons in a test environment before production
4. Document your code and share your discoveries with the community

Happy addon development!
