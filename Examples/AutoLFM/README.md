> [!NOTE]
> **Archived example.** This is a frozen snapshot of the AutoLFM addon, kept inside [WoW-Vanilla-Addon-Resources](https://github.com/NSO73/WoW-Vanilla-Addon-Resources) as a complete, real-world example of a WoW 1.12 / Turtle WoW addon. Turtle WoW has closed and the addon is no longer maintained. It illustrates the concepts documented in this repository: file separation, saved variables, XML templates, a command-bus (Maestro) architecture, and more. Developer documentation lives in [`_wiki/`](_wiki/). Links pointing to the original standalone GitHub repository may be dead.

# AutoLFM - Automated LFM Broadcaster for Turtle WoW
<p align="center">
  <img src="_assets/AutoLFM.gif" alt="AutoLFM Illustration"/>
</p>

## 📜 Description
AutoLFM is a Turtle WoW addon that automates the process of broadcasting "Looking For More" (LFM) messages for dungeons, raids, quests and more. This addon helps group leaders efficiently recruit party members without manual spam.

**🐢 Turtle WoW**
- Developed for Turtle WoW: includes all custom dungeons and raids from the server
- Interface design matches Turtle WoW's native LFG system
- Should also work on any WoW 1.12 (Vanilla) client, but custom Turtle WoW instances will appear regardless

<!-- TODO: When GitHub Wiki is activated, replace _wiki/*.md links with https://github.com/FSuhas/AutoLFM/wiki/* -->
## 📖 Documentation
- **Users**: [Installation & Usage Guide](_wiki/Installation-Usage.md) - How to install and use AutoLFM
- **Developers**: [Developer Guide](_wiki/Developer-Guide.md) - Architecture, best practices, and component registry

## ✨ Features
### 🎯 Content Selection
- Browse all Turtle WoW instances: vanilla dungeons/raids + custom content
- Multi-selection support with smart filtering by difficulty color (GRAY/GREEN/YELLOW/ORANGE/RED)
- Quest log integration: add quest/item/chat links via Shift+Click
- 5-tab navigation system with quick access via `/lfm` command or minimap button
- Variable group sizes for applicable raids (10-40 slider)
- Customizable minimum group size for dungeons and raids

### 📢 Broadcasting & Messages
- Adjustable broadcast interval (30-120 seconds) with multiple channel support
- Automatic start/stop based on group status
- Live message preview before broadcasting
- Two message modes: **Details mode** (auto-formatted) or **Custom mode** (fully customizable)
- Role management: Tank/Healer/DPS visual selector with automatic formatting
- Real-time statistics: duration timer, message count, next broadcast countdown
- Configurable preview display (1 or 2 lines)

### 💾 Presets System
- Save unlimited preset configurations with one click
- Preset data includes: content selection, group size, roles, channels, broadcast interval, and messages
- Load presets instantly to quickly switch between different LFM setups
- Rename, reorder, and delete presets
- Condensed view mode for easier preset management

### 🎨 Interface & Controls
- Eye-catching broadcast animation with color indicators
- Draggable minimap button with position memory
- One-click clear all selections with smart detection
- Tooltip guidance throughout the interface
- Optional dark mode (ShaguTweaks integration)
- Dry run mode for testing broadcasts without sending messages

### 🤖 Auto-Invite System
- Automatically respond to whisper keywords with group invitations
- Configurable invitation keywords (default: "+1")
- Random invitation messages for personality (or simple confirmation)
- Optional confirmation whispers to invited players
- Smart leader check: respond only when you're group leader (configurable)
- Real-time keyword matching and filtering

## 🚀 Quick Start
1. **Install**: Extract into your WoW `Interface/AddOns/` folder
2. **Enable**: Type `/lfm` in-game to open the interface (or click minimap button)
3. **Select Content**: Navigate to Dungeons/Raids/Quests tabs and check desired content
4. **Choose Roles**: Click Tank/Healer/DPS icons (optional)
5. **Set Message Mode**: Choose between Details (auto-formatted) or Custom message mode
6. **Configure**: Select channels and broadcast interval in Settings tab
7. **Save as Preset** (optional): Click "Presets" tab to save your setup for quick reuse
8. **Start Broadcasting**: Click the "Start" button
9. **Enable Auto-Invite** (optional): Go to Settings tab to configure auto-response keywords

For detailed instructions, see [Installation & Usage Guide](_wiki/Installation-Usage.md).

## 🏗️ Architecture
AutoLFM uses the **Maestro CQRS command bus system** for clean architecture:
- **Core/**: Framework (Maestro command bus, Events, Utils, Storage)
- **Components/**: System components
- **Logic/**: Business logic (Broadcaster, Content, Selection, State management)
- **UI/**: User interface (Templates, Content panels, Controls)

For detailed architecture, see [Developer Guide](_wiki/Developer-Guide.md).

## 💾 Configuration
Settings are automatically saved per character in `SavedVariables/AutoLFM.lua`:
- **Broadcasting:** Interval (30-120s), chat channels, General channel index
- **Content:** Dungeon filters (GRAY/GREEN/YELLOW/ORANGE/RED), default panel
- **Interface:** Minimap position, dark mode, message preview lines
- **Messages:** Simple vs Custom mode
- **Presets:** Saved presets, condensed view
- **Auto-Invite:** Keywords, random messages, reply confirmation
- **Other:** Dry run mode, welcome popup, hardcore detection

## 📸 Screenshots
<p align="center" width="100%">
  <a href="_assets/Screenshots/01-Dungeons.png"><img width="24%" src="_assets/Screenshots/01-Dungeons.png"></a>
  <a href="_assets/Screenshots/02-Raids.png"><img width="24%" src="_assets/Screenshots/02-Raids.png"></a>
  <a href="_assets/Screenshots/03-Quests.png"><img width="24%" src="_assets/Screenshots/03-Quests.png"></a>
  <a href="_assets/Screenshots/04-Details.png"><img width="24%" src="_assets/Screenshots/04-Details.png"></a>
</p>
<p align="center" width="100%">
  <a href="_assets/Screenshots/05-Custom.png"><img width="24%" src="_assets/Screenshots/05-Custom.png"></a>
  <a href="_assets/Screenshots/06-Presets.png"><img width="24%" src="_assets/Screenshots/06-Presets.png"></a>
  <a href="_assets/Screenshots/07-Autoinv.png"><img width="24%" src="_assets/Screenshots/07-Autoinv.png"></a>
  <a href="_assets/Screenshots/08-Settings.png"><img width="24%" src="_assets/Screenshots/08-Settings.png"></a>
</p>

## ℹ️ Information
- **Server**: Turtle WoW (compatible WoW 1.12 / Interface 11200)
- **Lua Version**: 5.0
- **External Libraries**: None
- **Quest Links Dependencies**: ShaguTweaks for links and pfQuest for information tooltip

## 📋 Project Status

The main contributor has moved on to other projects. The community is welcome to submit pull requests, report issues, or take over maintenance of the project.
