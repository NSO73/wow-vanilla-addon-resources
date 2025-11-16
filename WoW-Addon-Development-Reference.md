# Guide de Référence pour le Développement d'Addons WoW (Vanilla 1.12.1)

Ce document compile l'API WoW, les événements et les fonctions globales pour faciliter le développement d'addons pour World of Warcraft version 1.12.1 (Vanilla).

---

**⚠️ IMPORTANT - Lua 5.0 (WoW Vanilla 1.12)**

Ce guide est pour **WoW Vanilla 1.12** qui utilise **Lua 5.0**, pas Lua 5.1+.

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

1. [Introduction](#introduction)
2. [Notations et Conventions](#notations-et-conventions)
3. [API des Fonctions Globales](#api-des-fonctions-globales)
4. [Système d'Événements](#système-dévénements)
5. [Gestion des Frames](#gestion-des-frames)
6. [Bonnes Pratiques](#bonnes-pratiques)
7. [Exemples de Code](#exemples-de-code)

---

## Introduction

Ce guide sert de référence rapide pour le développement d'addons WoW Vanilla. Il couvre :
- Les fonctions API disponibles
- Les événements du jeu
- Les systèmes de frames et UI
- Les meilleures pratiques de développement

### Ressources Sources
- `apiwow` : Fonctions API détaillées par catégorie
- `global-api-wow.txt` : Liste complète des fonctions globales
- `wow-events.txt` : Catalogue complet des événements WoW

---

## Notations et Conventions

### Préfixes des Fonctions

- **UI** : Fonction implémentée en Lua dans l'interface utilisateur de Blizzard (FrameXML)
- **PROTECTED** : Fonction accessible uniquement aux addons signés par Blizzard
- **REMOVED** : Fonction qui n'est plus disponible

### Types de Retour Communs

- `nil` : Valeur nulle ou absence de résultat
- `1` / `nil` : Booléen (1 = true, nil = false)
- `"string"` : Chaîne de caractères
- `number` : Valeur numérique

---

## API des Fonctions Globales

### 1. Fonctions d'Action (Action Buttons)

Ces fonctions gèrent les barres d'action et leurs boutons.

```lua
-- Gestion des boutons d'action
ActionButtonDown(id)                    -- Presse un bouton d'action
ActionButtonUp(id)                      -- Relâche un bouton d'action
HasAction(slot)                         -- Vérifie si une action existe (retourne 1 ou nil)
UseAction(slot[, checkCursor[, onSelf]]) -- Utilise l'action du slot

-- Informations sur les actions
GetActionCooldown(slot)                 -- Retourne start, duration, enable
GetActionCount(slot)                    -- Nombre d'utilisations (potions, etc.)
GetActionText(slot)                     -- Texte de l'action (macros)
GetActionTexture(slot)                  -- Chemin de la texture

-- Tests d'état
IsActionInRange(slot)                   -- 1=oui, 0=non, nil=N/A
IsAttackAction(slot)                    -- 1 si action d'attaque
IsAutoRepeatAction(slot)                -- 1 si auto-répétition
IsCurrentAction(slot)                   -- 1 si action en cours
IsUsableAction(slot)                    -- 1 si utilisable
IsConsumableAction(slot)                -- 1 si consommable
IsEquippedAction(slot)                  -- 1 si nécessite équipement

-- Manipulation des actions
PickupAction(slot)                      -- Prend une action sur le curseur
PlaceAction(slot)                       -- Place l'action depuis le curseur
```

### 2. Fonctions d'Activité du Joueur

```lua
-- Combat et Duel
AttackTarget()                          -- Attaque la cible
AcceptDuel()                            -- Accepte un duel
CancelDuel()                            -- Refuse un duel
StartDuel("name")                       -- Défie un joueur
StartDuelUnit("unit")                   -- Défie une unité

-- Actions générales
SitOrStand()                            -- S'assoit ou se lève
TogglePVP()                             -- Active/désactive le PVP
ToggleSheath()                          -- Dégaine/rengaine les armes
RandomRoll(low, high)                   -- Lance un dé

-- Déconnexion
Logout()                                -- Se déconnecte
Quit()                                  -- Quitte le jeu
CancelLogout()                          -- Annule la déconnexion
```

### 3. Gestion des AddOns

```lua
-- Charger/Décharger des addons
LoadAddOn(index or "name")              -- Charge un addon LoD
IsAddOnLoaded(index or "name")          -- Vérifie si chargé
IsAddOnLoadOnDemand(index or "name")    -- Vérifie si LoD

-- Informations sur les addons
GetNumAddOns()                          -- Nombre d'addons
GetAddOnInfo(index or "name")           -- Infos sur un addon
GetAddOnMetadata(index, "variable")     -- Lit métadonnée du TOC
GetAddOnDependencies(index or "name")   -- Liste des dépendances

-- Activation/Désactivation
EnableAddOn(index or "name")            -- Active pour sessions futures
DisableAddOn(index or "name")           -- Désactive pour sessions futures
EnableAllAddOns()                       -- Active tous
DisableAllAddOns()                      -- Désactive tous
```

### 4. Fonctions de Communication

```lua
-- Messages de chat
SendChatMessage("text"[, "type"[, language[, target]]])
-- Types: "SAY", "YELL", "PARTY", "RAID", "GUILD", "OFFICER", "WHISPER", "CHANNEL"

-- Canaux de chat
JoinChannelByName("channel"[, "password"[, frameId]])
LeaveChannelByName("channel")
ListChannels()                          -- Liste tous les canaux
GetChannelList()                        -- Canaux rejoints
GetChannelName("channel" or index)      -- Nom d'un canal

-- Communication addon (v1.12+)
SendAddonMessage("prefix", "text", "type")

-- Emotes
DoEmote("emote"[, "target"])           -- Exécute une emote

-- Langues
GetDefaultLanguage("unit")              -- Langue par défaut
GetLanguageByIndex(index)               -- Langue par index
GetNumLaguages()                        -- Nombre de langues
```

### 5. Gestion des Conteneurs (Sacs)

```lua
-- Informations sur les sacs
GetBagName(bagID)                       -- Nom du sac
GetContainerNumSlots(bagID)             -- Nombre de slots
GetContainerItemInfo(bagID, slot)       -- Infos sur item
GetContainerItemLink(bagID, slot)       -- ItemLink
GetContainerItemCooldown(bagID, slot)   -- Cooldown de l'item

-- Utilisation des items
UseContainerItem(bagID, slot[, onSelf]) -- Utilise un item
PickupContainerItem(bagID, slot)        -- Prend sur curseur
SplitContainerItem(bagID, slot, amount) -- Divise un stack

-- Interface
ToggleBackpack()                        -- Ouvre/ferme le sac à dos
ToggleBag(bagID)                        -- Ouvre/ferme un sac
OpenAllBags()                           -- Ouvre/ferme tous les sacs

-- Placement d'items
PutItemInBackpack()                     -- Place dans sac à dos
PutItemInBag(inventoryId)               -- Place dans sac spécifique
```

### 6. Fonctions de Personnage

```lua
-- Statistiques
GetMoney()                              -- Argent en cuivre
GetComboPoints()                        -- Points de combo
GetBlockChance()                        -- % chance de blocage
GetDodgeChance()                        -- % chance d'esquive
GetParryChance()                        -- % chance de parade

-- État du repos
IsResting()                             -- En repos ?
GetRestState()                          -- État de repos
GetXPExhaustion()                       -- XP de repos restante

-- Résurrection
AcceptResurrect()                       -- Accepte résurrection
DeclineResurrect()                      -- Refuse résurrection
RetrieveCorpse()                        -- Récupère le corps
ResurrectHasSickness()                  -- A le mal de résurrection ?
UseSoulstone()                          -- Utilise pierre d'âme

-- Pierre de foyer
GetBindLocation()                       -- Lieu de la pierre
ConfirmBinder()                         -- Confirme nouveau lieu
CheckBinderDist()                       -- Assez proche ?
```

### 7. Fonctions de Groupe et Raid

```lua
-- Gestion du groupe
AcceptGroup()                           -- Accepte invitation
DeclineGroup()                          -- Refuse invitation
InviteByName("name")                    -- Invite un joueur
InviteToParty("unit")                   -- Invite une unité
LeaveParty()                            -- Quitte le groupe
UninviteByName("name")                  -- Expulse un joueur

-- Informations groupe
GetNumPartyMembers()                    -- Nombre de membres
GetPartyMember(index)                   -- UnitID du membre
IsPartyLeader()                         -- Est le chef ?
GetPartyLeaderIndex()                   -- Index du chef
UnitInParty("unit")                     -- Unité dans groupe ?

-- Leadership
PromoteByName("name")                   -- Promouvoir
PromoteToPartyLeader("unit")            -- Promouvoir chef
ConvertToRaid()                         -- Convertir en raid

-- Loot
GetLootMethod()                         -- Méthode de butin
SetLootMethod("method"[, "master" or threshold])
GetLootThreshold()                      -- Seuil de butin
SetLootThreshold(itemQuality)           -- Définir seuil
GetMasterLootCandidate(index)           -- Candidat pour ML

-- Ready check
DoReadyCheck()                          -- Lance un ready check
ConfirmReadyCheck(isReady)              -- Répond au ready check
```

### 8. Fonctions d'Unité (UnitID)

Les UnitIDs sont des identifiants comme : `"player"`, `"target"`, `"party1"` à `"party4"`, `"raid1"` à `"raid40"`, `"pet"`, `"mouseover"`, etc.

```lua
-- Informations de base
UnitName("unit")                        -- Nom de l'unité
UnitLevel("unit")                       -- Niveau
UnitClass("unit")                       -- Classe
UnitRace("unit")                        -- Race
UnitSex("unit")                         -- Sexe (1=inconnu, 2=M, 3=F)
UnitFactionGroup("unit")                -- Faction ("Alliance"/"Horde")

-- État de santé
UnitHealth("unit")                      -- Points de vie actuels
UnitHealthMax("unit")                   -- PV maximum
UnitMana("unit")                        -- Mana/Energy/Rage
UnitManaMax("unit")                     -- Maximum
UnitIsDead("unit")                      -- Est mort ?
UnitIsGhost("unit")                     -- Est un fantôme ?

-- État
UnitIsPlayer("unit")                    -- Est un joueur ?
UnitIsUnit("unit1", "unit2")            -- unit1 == unit2 ?
UnitInParty("unit")                     -- Dans le groupe ?
UnitInRaid("unit")                      -- Dans le raid ?
UnitIsConnected("unit")                 -- Est connecté ?
UnitAffectingCombat("unit")             -- En combat ?

-- Buffs et Debuffs
UnitBuff("unit", index[, showCastable]) -- Info buff
UnitDebuff("unit", index[, showDispellable]) -- Info debuff

-- Ciblage
UnitCanAttack("player", "unit")         -- Peut attaquer ?
UnitCanAssist("player", "unit")         -- Peut assister ?
UnitIsEnemy("player", "unit")           -- Est ennemi ?
UnitIsFriend("player", "unit")          -- Est ami ?
```

### 9. Fonctions de Sort

```lua
-- Livre de sorts
GetNumSpellTabs()                       -- Nombre d'onglets
GetSpellTabInfo(index)                  -- Info onglet
GetSpellName(spellID, "bookType")       -- Nom du sort
GetSpellTexture(spellID, "bookType")    -- Texture du sort
GetSpellCooldown(spellID, "bookType")   -- Cooldown

-- Lancer des sorts
CastSpell(spellID, "bookType")          -- Lance un sort
CastSpellByName("name"[, onSelf])       -- Lance par nom
SpellStopCasting()                      -- Annule le cast

-- Manipulation
PickupSpell(spellID, "bookType")        -- Prend sur curseur

-- Note: bookType peut être "spell" ou "pet"
```

### 10. Fonctions de Quête

```lua
-- Liste des quêtes
GetNumQuestLogEntries()                 -- Nombre de quêtes
GetQuestLogTitle(index)                 -- Titre de la quête
SelectQuestLogEntry(index)              -- Sélectionne une quête
GetQuestLogSelection()                  -- Quête sélectionnée

-- Détails de quête
GetNumQuestLeaderBoards(index)          -- Nombre d'objectifs
GetQuestLogLeaderBoard(index)           -- Détails objectif

-- Interactions NPC
GetNumQuestChoices()                    -- Nombre de récompenses au choix
GetNumQuestRewards()                    -- Nombre de récompenses fixes
GetQuestReward(index)                   -- Choisit une récompense
```

### 11. Fonctions d'Inventaire

```lua
-- Slots d'équipement
GetInventorySlotInfo("slotName")        -- ID du slot
GetInventoryItemLink("unit", slotID)    -- ItemLink équipé
GetInventoryItemTexture("unit", slotID) -- Texture
GetInventoryItemCount("unit", slotID)   -- Nombre (munitions)
GetInventoryItemCooldown("unit", slotID) -- Cooldown

-- Manipulation
PickupInventoryItem(invSlot)            -- Prend sur curseur
EquipCursorItem(invSlot)                -- Équipe depuis curseur
AutoEquipCursorItem()                   -- Auto-équipe

-- Slots: "HeadSlot", "NeckSlot", "ShoulderSlot", "BackSlot", "ChestSlot",
-- "ShirtSlot", "TabardSlot", "WristSlot", "HandsSlot", "WaistSlot",
-- "LegsSlot", "FeetSlot", "Finger0Slot", "Finger1Slot", "Trinket0Slot",
-- "Trinket1Slot", "MainHandSlot", "SecondaryHandSlot", "RangedSlot", "AmmoSlot"
```

### 12. Fonctions de Ciblage

```lua
-- Ciblage basique
TargetUnit("unit")                      -- Cible une unité
TargetByName("name"[, exactMatch])      -- Cible par nom
ClearTarget()                           -- Efface la cible
AssistUnit("unit")                      -- Assiste une unité

-- Ciblage contextuel
TargetNearestEnemy([reverseFlag])       -- Ennemi le plus proche
TargetNearestFriend([reverseFlag])      -- Ami le plus proche
TargetLastTarget()                      -- Dernière cible
TargetLastEnemy()                       -- Dernier ennemi

-- Ciblage par type
TargetNearestPartyMember()              -- Membre groupe proche
TargetNearestRaidMember()               -- Membre raid proche
```

### 13. Fonctions de Camera

```lua
-- Zoom
CameraZoomIn(increment)                 -- Zoom avant
CameraZoomOut(increment)                -- Zoom arrière

-- Rotation
MoveViewLeftStart()                     -- Rotation gauche (début)
MoveViewLeftStop()                      -- Rotation gauche (fin)
MoveViewRightStart()                    -- Rotation droite (début)
MoveViewRightStop()                     -- Rotation droite (fin)
MoveViewUpStart()                       -- Rotation haut (début)
MoveViewUpStop()                        -- Rotation haut (fin)
MoveViewDownStart()                     -- Rotation bas (début)
MoveViewDownStop()                      -- Rotation bas (fin)

-- Vues prédéfinies
SetView(index)                          -- Vue 1-5
SaveView(index)                         -- Sauvegarde vue
ResetView(index)                        -- Réinitialise vue
NextView()                              -- Vue suivante
PrevView()                              -- Vue précédente

-- MouseLook
IsMouselooking()                        -- En mouselook ?
MouselookStart()                        -- Démarre mouselook
MouselookStop()                         -- Arrête mouselook
```

### 14. Fonctions de Temps et Debug

```lua
-- Temps
GetTime()                               -- Temps en secondes depuis démarrage
time()                                  -- Timestamp UNIX
date(format[, time])                    -- Formatte une date

-- Debug
debugprofilestart()                     -- Démarre timer de profiling
debugprofilestop()                      -- Temps écoulé en ms
debugstack([start[, count1, count2]])   -- Stack trace
DEFAULT_CHAT_FRAME:AddMessage("text")   -- Affiche dans chat
```

### 15. Fonctions de Curseur

```lua
-- État du curseur
CursorHasItem()                         -- A un item ?
CursorHasMoney()                        -- A de l'argent ?
CursorHasSpell()                        -- A un sort ?
GetCursorPosition()                     -- Position X, Y

-- Manipulation
ClearCursor()                           -- Vide le curseur
ResetCursor()                           -- Réinitialise
DeleteCursorItem()                      -- Détruit l'item
DropCursorMoney()                       -- Lâche l'argent

-- Argent
GetCursorMoney()                        -- Montant sur curseur
PickupPlayerMoney(amount)               -- Prend argent
DropCursorMoney()                       -- Lâche argent
```

---

## Système d'Événements

### Enregistrement et Gestion des Événements

```lua
-- Dans un frame
frame:RegisterEvent("EVENT_NAME")       -- Enregistre un événement
frame:UnregisterEvent("EVENT_NAME")     -- Désenregistre
frame:UnregisterAllEvents()             -- Désenregistre tous
frame:IsEventRegistered("EVENT_NAME")   -- Vérifie enregistrement

-- Handler d'événement
frame:SetScript("OnEvent", function(self, event, ...)
    if event == "EVENT_NAME" then
        local arg1, arg2 = ...
        -- Traiter l'événement
    end
end)
```

### Événements Principaux par Catégorie

#### 1. Événements de Connexion/Déconnexion

```
PLAYER_LOGIN
    - Déclenché au login, avant PLAYER_ENTERING_WORLD
    - Premier événement utile pour initialisation

PLAYER_ENTERING_WORLD
    - Déclenché quand le joueur entre dans le monde
    - Aussi déclenché lors de téléportations/instances/reloads

PLAYER_LEAVING_WORLD
    - Déclenché lors de la déconnexion

PLAYER_LOGOUT
    - Après PLAYER_LEAVING_WORLD, avant sauvegarde variables

VARIABLES_LOADED
    - SavedVariables chargées en mémoire

ADDON_LOADED
    - arg1: nom de l'addon chargé
    - Déclenché pour chaque addon
```

#### 2. Événements de Combat

```
PLAYER_ENTER_COMBAT
    - Déclenché au début de l'auto-attaque
    - N'inclut PAS les sorts ou l'aggro

PLAYER_LEAVE_COMBAT
    - Déclenché à la fin de l'auto-attaque

PLAYER_REGEN_DISABLED
    - Déclenché quand on reçoit l'aggro
    - Plus fiable pour détecter le combat

PLAYER_REGEN_ENABLED
    - Déclenché quelques secondes après la fin du combat
    - Régénération normale reprend

PLAYER_DEAD
    - Le joueur est mort

PLAYER_ALIVE
    - Le joueur est vivant (après release, feign death, rez avant release)

PLAYER_UNGHOST
    - Le joueur est ressuscité après release
```

#### 3. Événements de Chat

```
CHAT_MSG_SAY
    - arg1: message, arg2: auteur, arg3: langue

CHAT_MSG_YELL
    - arg1: message, arg2: auteur, arg3: langue

CHAT_MSG_PARTY
    - arg1: message, arg2: auteur, arg3: langue

CHAT_MSG_RAID
    - Message dans le raid

CHAT_MSG_GUILD
    - arg1: message, arg2: auteur, arg3: langue

CHAT_MSG_OFFICER
    - arg1: message, arg2: auteur, arg3: langue

CHAT_MSG_WHISPER
    - arg1: message, arg2: auteur, arg6: statut (AFK/DND)

CHAT_MSG_WHISPER_INFORM
    - Whisper envoyé
    - arg1: message, arg2: destinataire

CHAT_MSG_CHANNEL
    - arg1: message, arg2: auteur, arg4: canal avec numéro
    - arg8: numéro canal, arg9: nom canal sans numéro

CHAT_MSG_ADDON
    - Message inter-addon (v1.12+)

CHAT_MSG_SYSTEM
    - Messages système (jaunes)
    - arg1: contenu du message
```

#### 4. Événements d'Unité

```
UNIT_HEALTH
    - arg1: unitID
    - PV de l'unité changent

UNIT_MANA / UNIT_ENERGY / UNIT_RAGE
    - arg1: unitID
    - Ressource change

UNIT_MAXHEALTH / UNIT_MAXMANA
    - Maximum change

UNIT_AURA
    - arg1: unitID
    - Buff/debuff gagné ou perdu
    - Aussi déclenché au changement de cible

UNIT_COMBAT
    - arg1: unitID, arg2: action, arg3: critique, arg4: dégâts, arg5: type

UNIT_LEVEL
    - arg1: unitID
    - Niveau soumis/changé

UNIT_NAME_UPDATE
    - arg1: unitID
    - Nom change

PLAYER_TARGET_CHANGED
    - Cible du joueur change

PLAYER_PET_CHANGED
    - Familier change
```

#### 5. Événements de Groupe/Raid

```
PARTY_MEMBERS_CHANGED
    - Composition du groupe change
    - Déclenché plusieurs fois lors d'invitation

PARTY_LEADER_CHANGED
    - Leader du groupe change

PARTY_LOOT_METHOD_CHANGED
    - Méthode de butin change

RAID_ROSTER_UPDATE
    - Composition du raid change
    - Aussi si règles de butin changent

PARTY_MEMBER_ENABLE
    - arg1: nom du joueur
    - Membre connecté/vivant

PARTY_MEMBER_DISABLE
    - arg1: nom du joueur
    - Membre déconnecté/mort
```

#### 6. Événements d'Inventaire et Sacs

```
BAG_UPDATE
    - arg1: bagID
    - Contenu d'un sac change

BAG_UPDATE_COOLDOWN
    - arg1: bagID
    - Cooldown dans le sac

ITEM_LOCK_CHANGED
    - Item verrouillé/déverrouillé

UNIT_INVENTORY_CHANGED
    - arg1: unitID
    - Équipement change

PLAYER_MONEY
    - Argent change

ITEM_PUSH
    - arg1: taille stack, arg2: icône
    - Item créé/ramassé
```

#### 7. Événements de Sorts

```
SPELLCAST_START
    - arg1: nom sort, arg2: durée en ms
    - Cast démarre

SPELLCAST_STOP
    - Cast se termine ou s'annule

SPELLCAST_FAILED
    - Cast échoue

SPELLCAST_INTERRUPTED
    - Cast interrompu

SPELLCAST_DELAYED
    - arg1: délai
    - Cast retardé

SPELLCAST_CHANNEL_START
    - arg1: durée en ms, arg2: nom sort
    - Channeling démarre

SPELLCAST_CHANNEL_UPDATE
    - arg1: durée restante en ms
    - Channeling en cours

SPELLCAST_CHANNEL_STOP
    - Channeling se termine

SPELL_UPDATE_COOLDOWN
    - Cooldown de sort démarre

LEARNED_SPELL_IN_TAB
    - arg1: numéro onglet
    - Nouveau sort appris
```

#### 8. Événements d'Interface

```
ACTIONBAR_SLOT_CHANGED
    - arg1: numéro slot
    - Contenu barre d'action change

ACTIONBAR_PAGE_CHANGED
    - Page de barre d'action change

ACTIONBAR_UPDATE_COOLDOWN
    - Cooldown sur barre d'action

UPDATE_BINDINGS
    - Raccourcis clavier changent

UPDATE_MACROS
    - Macros changent

PLAYER_AURAS_CHANGED
    - Auras du joueur changent
    - Aussi pour changements forme druide

PLAYER_COMBO_POINTS
    - Points de combo changent
    - Utiliser GetComboPoints() pour le nombre
```

#### 9. Événements de Quête

```
QUEST_LOG_UPDATE
    - Journal de quête change
    - Très fréquent

QUEST_DETAIL
    - Détails de quête affichés

QUEST_PROGRESS
    - Progression de quête affichée

QUEST_COMPLETE
    - Quête complète, avant validation

QUEST_FINISHED
    - Fenêtre de quête change ou ferme

QUEST_GREETING
    - Quête offerte
```

#### 10. Événements de Courrier

```
MAIL_SHOW
    - Boîte aux lettres ouverte

MAIL_CLOSED
    - Boîte aux lettres fermée

MAIL_INBOX_UPDATE
    - Contenu boîte change

MAIL_SEND_SUCCESS
    - Courrier envoyé avec succès

UPDATE_PENDING_MAIL
    - Notification de nouveau courrier
```

#### 11. Événements de Marchand/Vendeur

```
MERCHANT_SHOW
    - Fenêtre marchand ouverte

MERCHANT_CLOSED
    - Fenêtre marchand fermée

MERCHANT_UPDATE
    - Inventaire marchand change
```

#### 12. Événements de Zone

```
ZONE_CHANGED
    - Zone change (texte minimap)

ZONE_CHANGED_INDOORS
    - Zone intérieure change

ZONE_CHANGED_NEW_AREA
    - Nouvelle région (nouveaux canaux)

MINIMAP_ZONE_CHANGED
    - Zone minimap change
```

#### 13. Événements de Butin

```
LOOT_OPENED
    - Fenêtre de butin ouverte

LOOT_CLOSED
    - Fenêtre de butin fermée

LOOT_SLOT_CLEARED
    - arg1: slot
    - Item de butin retiré

START_LOOT_ROLL
    - arg1: rollID, arg2: temps
    - Butin en roll
```

#### 14. Événements de PvP/Champs de Bataille

```
UPDATE_BATTLEFIELD_STATUS
    - Statut champ de bataille change

UPDATE_BATTLEFIELD_SCORE
    - Scores changent

PLAYER_PVP_KILLS_CHANGED
    - Nombre de kills PvP change

PLAYER_FLAGS_CHANGED
    - arg1: unitID
    - Flags changent (AFK, DND, etc.)
```

---

## Gestion des Frames

### Création de Frames

```lua
-- Via XML (recommandé pour UI complexe)
<Frame name="MonFrame" parent="UIParent">
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
                DEFAULT_CHAT_FRAME:AddMessage("Addon chargé!");
            end
        </OnEvent>
    </Scripts>
</Frame>

-- Via Lua (pour frames dynamiques)
local frame = CreateFrame("Frame", "MonFrameLua", UIParent)
frame:SetWidth(200)
frame:SetHeight(100)
frame:SetPoint("CENTER")

-- Enregistrement d'événements
frame:RegisterEvent("PLAYER_LOGIN")
frame:SetScript("OnEvent", function(self, event, ...)
    if event == "PLAYER_LOGIN" then
        DEFAULT_CHAT_FRAME:AddMessage("Frame créé dynamiquement!")
    end
end)
```

### Types de Frames

```
Frame           - Frame basique, conteneur
Button          - Bouton cliquable
CheckButton     - Case à cocher
EditBox         - Champ de saisie texte
ScrollFrame     - Frame avec scroll
Slider          - Curseur de valeur
StatusBar       - Barre de progression
Texture         - Image/texture
FontString      - Texte
```

### Scripts de Frame

```lua
frame:SetScript("OnLoad", function(self)
    -- Initialisation
end)

frame:SetScript("OnShow", function(self)
    -- Quand affiché
end)

frame:SetScript("OnHide", function(self)
    -- Quand caché
end)

frame:SetScript("OnUpdate", function(self, elapsed)
    -- Chaque frame (~60 fps)
    -- elapsed = temps depuis dernière update
end)

frame:SetScript("OnEvent", function(self, event, ...)
    -- Gestionnaire d'événements
end)

-- Pour boutons
button:SetScript("OnClick", function(self, button)
    -- button = "LeftButton", "RightButton", etc.
end)

button:SetScript("OnEnter", function(self)
    -- Souris entre dans le bouton
end)

button:SetScript("OnLeave", function(self)
    -- Souris sort du bouton
end)
```

### Positionnement (Anchors)

```lua
frame:SetPoint("point"[, relativeTo[, "relativePoint"[, xOffset, yOffset]]])

-- Points: "TOPLEFT", "TOP", "TOPRIGHT", "LEFT", "CENTER", "RIGHT",
--         "BOTTOMLEFT", "BOTTOM", "BOTTOMRIGHT"

-- Exemples
frame:SetPoint("CENTER")                        -- Centre de l'écran
frame:SetPoint("TOPLEFT", 10, -10)              -- Haut gauche + offset
frame:SetPoint("BOTTOM", UIParent, "BOTTOM", 0, 50) -- Bas écran + 50px
frame:SetPoint("LEFT", autreFrame, "RIGHT", 5, 0)   -- À droite d'un frame
```

### Textures et FontStrings

```lua
-- Texture
local texture = frame:CreateTexture(nil, "BACKGROUND")
texture:SetTexture("Interface\\Icons\\INV_Misc_QuestionMark")
texture:SetAllPoints(frame)  -- Remplit tout le frame

-- FontString
local text = frame:CreateFontString(nil, "OVERLAY", "GameFontNormal")
text:SetPoint("CENTER")
text:SetText("Mon texte")
text:SetTextColor(1, 1, 1)  -- RGB (0-1)
```

---

## Bonnes Pratiques

### 1. Structure d'un Addon

```
MonAddon/
├── MonAddon.toc        # Fichier de configuration
├── MonAddon.lua        # Code principal
├── MonAddon.xml        # Interface (optionnel)
├── Localization.lua    # Traductions (optionnel)
└── README.txt          # Documentation
```

### 2. Fichier TOC

```toc
## Interface: 11200
## Title: Mon Super Addon
## Notes: Description de mon addon
## Author: VotreNom
## Version: 1.0.0
## SavedVariables: MonAddonDB
## SavedVariablesPerCharacter: MonAddonCharDB

# Fichiers à charger (ordre important!)
Localization.lua
MonAddon.xml
MonAddon.lua
```

### 3. Initialisation Propre

```lua
-- Créer un namespace pour éviter conflits globaux
-- Note Lua 5.0: pas de ... automatique dans les fichiers .lua
MonAddon = MonAddon or {}

-- Frame principal
local frame = CreateFrame("Frame")

-- Variables locales pour performance
local DEFAULT_CHAT_FRAME = DEFAULT_CHAT_FRAME
local UnitName = UnitName
local GetTime = GetTime

-- Table de gestion des événements
local events = {}

function events:PLAYER_LOGIN()
    -- Initialisation au login
    if not MonAddonDB then
        MonAddonDB = {
            enabled = true,
            options = {}
        }
    end
    DEFAULT_CHAT_FRAME:AddMessage("Mon Addon chargé!")
end

function events:PLAYER_ENTERING_WORLD()
    -- Actions à l'entrée dans le monde
end

-- Dispatcher d'événements (Lua 5.0)
frame:SetScript("OnEvent", function()
    -- En Lua 5.0: event, arg1, arg2, etc. sont globaux dans le handler
    -- 'this' fait référence au frame
    if events[event] then
        -- Passer les arguments courants
        events[event](this, arg1, arg2, arg3, arg4, arg5)
    end
end)

-- Enregistrement des événements
for event, _ in pairs(events) do
    frame:RegisterEvent(event)
end
```

### 4. Gestion des SavedVariables

```lua
-- Valeurs par défaut
local defaults = {
    enabled = true,
    scale = 1.0,
    position = {
        point = "CENTER",
        x = 0,
        y = 0
    }
}

-- Initialisation avec merge
function InitDB()
    MonAddonDB = MonAddonDB or {}

    -- Copier les defaults manquants
    for k, v in pairs(defaults) do
        if MonAddonDB[k] == nil then
            if type(v) == "table" then
                MonAddonDB[k] = {}
                for k2, v2 in pairs(v) do
                    MonAddonDB[k][k2] = v2
                end
            else
                MonAddonDB[k] = v
            end
        end
    end
end
```

### 5. Performance et Optimisation

```lua
-- Mettre en cache les fonctions fréquemment utilisées
local gsub = string.gsub
local format = string.format
local floor = math.floor
local pairs = pairs
local ipairs = ipairs

-- Éviter les créations répétées
local frame = CreateFrame("Frame")  -- UNE FOIS
-- PAS dans OnUpdate ou événements fréquents!

-- Throttling pour OnUpdate
local updateInterval = 0.1  -- 10 fois par seconde
local timeSinceLastUpdate = 0

frame:SetScript("OnUpdate", function(self, elapsed)
    timeSinceLastUpdate = timeSinceLastUpdate + elapsed

    if timeSinceLastUpdate >= updateInterval then
        -- Code qui s'exécute toutes les 0.1 secondes
        timeSinceLastUpdate = 0
    end
end)

-- Unregister les événements non utilisés
frame:UnregisterEvent("EVENT_PAS_UTILISE")
```

### 6. Gestion des Erreurs

```lua
-- Protected call pour éviter les erreurs qui cassent tout
local success, result = pcall(function()
    -- Code qui peut échouer
    return DoSomethingRisky()
end)

if not success then
    DEFAULT_CHAT_FRAME:AddMessage("Erreur: " .. tostring(result))
end

-- Validation des paramètres
function MonAddon:DoSomething(unit)
    if not unit or not UnitExists(unit) then
        return false, "Unité invalide"
    end

    -- Suite du code
    return true
end
```

### 7. Slash Commands

```lua
SLASH_MONADDON1 = "/monaddon"
SLASH_MONADDON2 = "/ma"

SlashCmdList["MONADDON"] = function(msg)
    msg = msg:lower():trim()

    if msg == "show" then
        MonAddonFrame:Show()
    elseif msg == "hide" then
        MonAddonFrame:Hide()
    elseif msg == "toggle" then
        if MonAddonFrame:IsShown() then
            MonAddonFrame:Hide()
        else
            MonAddonFrame:Show()
        end
    else
        DEFAULT_CHAT_FRAME:AddMessage("Commandes disponibles:")
        DEFAULT_CHAT_FRAME:AddMessage("/monaddon show - Afficher")
        DEFAULT_CHAT_FRAME:AddMessage("/monaddon hide - Cacher")
        DEFAULT_CHAT_FRAME:AddMessage("/monaddon toggle - Basculer")
    end
end
```

---

## Exemples de Code

### Exemple 1: Tracker de Cooldown

```lua
local frame = CreateFrame("Frame")
local cooldowns = {}

-- Surveiller les cooldowns
frame:RegisterEvent("SPELL_UPDATE_COOLDOWN")
frame:SetScript("OnEvent", function(self, event)
    -- Scanner tous les sorts
    local i = 1
    while true do
        local name, rank = GetSpellName(i, BOOKTYPE_SPELL)
        if not name then break end

        local start, duration = GetSpellCooldown(i, BOOKTYPE_SPELL)

        if duration > 1.5 then  -- Ignorer GCD
            local key = name .. (rank or "")
            if not cooldowns[key] then
                cooldowns[key] = {
                    start = start,
                    duration = duration,
                    name = name,
                    rank = rank
                }
                DEFAULT_CHAT_FRAME:AddMessage(
                    name .. " en cooldown pour " .. duration .. "s"
                )
            end
        end

        i = i + 1
    end
end)

-- Nettoyer les cooldowns expirés
frame:SetScript("OnUpdate", function(self, elapsed)
    local now = GetTime()
    for k, cd in pairs(cooldowns) do
        if now >= cd.start + cd.duration then
            cooldowns[k] = nil
        end
    end
end)
```

### Exemple 2: Détection de Raid Boss

```lua
local frame = CreateFrame("Frame")
local bossNames = {
    ["Ragnaros"] = true,
    ["Onyxia"] = true,
    ["Nefarian"] = true,
    -- Ajouter d'autres boss
}

frame:RegisterEvent("PLAYER_TARGET_CHANGED")
frame:SetScript("OnEvent", function(self, event)
    if UnitExists("target") then
        local name = UnitName("target")
        local level = UnitLevel("target")

        -- Boss = niveau -1 ou dans la liste
        if level == -1 or bossNames[name] then
            DEFAULT_CHAT_FRAME:AddMessage(
                "BOSS DÉTECTÉ: " .. name,
                1, 0, 0  -- Rouge
            )

            -- Actions supplémentaires
            PlaySound("RaidWarning")
        end
    end
end)
```

### Exemple 3: Auto-Repair

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
                        "Items réparés pour %dg %ds %dc",
                        gold, silver, copper
                    ),
                    1, 1, 0
                )
            else
                DEFAULT_CHAT_FRAME:AddMessage(
                    "Pas assez d'argent pour réparer!",
                    1, 0, 0
                )
            end
        end
    end
end)
```

### Exemple 4: Tracker de Buff de Groupe

```lua
local frame = CreateFrame("Frame")
local buffToTrack = "Power Word: Fortitude"  -- Exemple

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

    -- Vérifier le joueur
    if not CheckUnitBuff("player") then
        table.insert(missing, UnitName("player"))
    end

    -- Vérifier le groupe
    for i = 1, GetNumPartyMembers() do
        local unit = "party" .. i
        if not CheckUnitBuff(unit) then
            table.insert(missing, UnitName(unit))
        end
    end

    if table.getn(missing) > 0 then
        DEFAULT_CHAT_FRAME:AddMessage(
            "Manque " .. buffToTrack .. ": " .. table.concat(missing, ", ")
        )
    end
end

-- Vérifier quand la composition change
frame:RegisterEvent("PARTY_MEMBERS_CHANGED")
frame:RegisterEvent("UNIT_AURA")
frame:SetScript("OnEvent", function(self, event, arg1)
    if event == "PARTY_MEMBERS_CHANGED" or
       (event == "UNIT_AURA" and (arg1 == "player" or arg1:find("party"))) then
        ScanPartyBuffs()
    end
end)
```

### Exemple 5: Calculateur de DPS Simple

```lua
local frame = CreateFrame("Frame")
local damage = 0
local combatStart = nil

frame:RegisterEvent("CHAT_MSG_COMBAT_SELF_HITS")
frame:RegisterEvent("PLAYER_REGEN_DISABLED")
frame:RegisterEvent("PLAYER_REGEN_ENABLED")

frame:SetScript("OnEvent", function(self, event, arg1)
    if event == "PLAYER_REGEN_DISABLED" then
        -- Combat commence
        damage = 0
        combatStart = GetTime()

    elseif event == "PLAYER_REGEN_ENABLED" then
        -- Combat termine
        if combatStart then
            local duration = GetTime() - combatStart
            local dps = damage / duration

            DEFAULT_CHAT_FRAME:AddMessage(
                string.format(
                    "Combat: %.1fs | Dégâts: %d | DPS: %.1f",
                    duration, damage, dps
                ),
                1, 1, 0
            )

            combatStart = nil
        end

    elseif event == "CHAT_MSG_COMBAT_SELF_HITS" then
        -- Parser les dégâts depuis le message
        -- Format: "Your Attack hits Enemy for X."
        local dmg = arg1:match("for (%d+)")
        if dmg then
            damage = damage + tonumber(dmg)
        end
    end
end)
```

---

## Ressources et Références

### Fonctions Utiles Non Documentées

```lua
-- String helpers
string.trim(s)                          -- Enlève espaces début/fin
string.split(sep, str)                  -- Divise string (v1.11+)

-- Table helpers
table.getn(t)                           -- Taille de table (#t en Lua 5.1+)

-- UI helpers
UIParent                                -- Frame parent de l'UI
WorldFrame                              -- Frame du monde 3D

-- Couleurs de qualité d'item
ITEM_QUALITY_COLORS[quality]            -- quality = 0-7
```

### Codes de Qualité d'Item

```
0 = Pauvre (Gris)
1 = Commun (Blanc)
2 = Inhabituel (Vert)
3 = Rare (Bleu)
4 = Épique (Violet)
5 = Légendaire (Orange)
6 = Artéfact (Jaune/Or)
7 = Héritage (Blizzard uniquement)
```

### IDs de Conteneurs

```
0  = Sac à dos
1  = Premier sac
2  = Deuxième sac
3  = Troisième sac
4  = Quatrième sac
-1 = Banque
5  = Premier sac de banque
6  = Deuxième sac de banque
...
11 = Septième sac de banque
```

### Codes de Classe

```lua
local _, englishClass = UnitClass("player")
-- englishClass peut être:
-- "WARRIOR", "PALADIN", "HUNTER", "ROGUE", "PRIEST",
-- "SHAMAN", "MAGE", "WARLOCK", "DRUID"
```

### Liens Utiles

- API Wiki (historique): https://wowwiki-archive.fandom.com/wiki/World_of_Warcraft_API
- Forums de développement d'addons
- Outils de développement: WoW AddOn Studio, Notepad++, VSCode

---

## Conclusion

Ce document est un point de départ pour le développement d'addons WoW Vanilla. Pour des informations plus détaillées:

1. Examinez les addons existants de qualité (comme CTMod, Cosmos, etc.)
2. Consultez les fichiers FrameXML de Blizzard dans le dossier d'installation
3. Testez vos addons dans un environnement de test avant production
4. Documentez votre code et partagez vos découvertes avec la communauté

Bon développement d'addons!

---

*Document généré à partir de: apiwow, global-api-wow.txt, wow-events.txt*
*Version du jeu: World of Warcraft 1.12.1 (Vanilla)*
*Dernière mise à jour: 2025*
