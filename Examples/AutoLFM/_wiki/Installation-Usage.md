# AutoLFM - Installation & Usage Guide

## Quick Links

- **Getting Started**: [Installation](#-installation) | [Basic Usage](#-basic-usage)
- **Developers**: [Dev Guide](Developer-Guide.md)
- **Support**: [Troubleshooting](#-troubleshooting) | [GitHub Issues](https://github.com/FSuhas/AutoLFM/issues)

---

## 📦 Installation

### Requirements
- Turtle WoW (ou tout client WoW Vanilla 1.12)
- AddOn compatible folder

### Method 1: Manual Installation

1. **Download** the AutoLFM addon from [GitHub](https://github.com/FSuhas/AutoLFM)
2. **Extract** the folder to your World of Warcraft `Interface/AddOns/` directory
3. **Restart** World of Warcraft
4. **Verify**: You should see "AutoLFM" in your AddOns list at login

### Method 2: TurtleWoW Launcher

1. Open the **TurtleWoW Launcher**
2. Navigate to the **AddOns** section
3. Add new addon and paste the repository URL: `https://github.com/FSuhas/AutoLFM`
4. Click **Install**

### Directory Structure
```
World of Warcraft/Interface/AddOns/
├── AutoLFM/
│   ├── AutoLFM.toc
│   ├── Core/
│   ├── Components/
│   ├── Logic/
│   ├── UI/
│   └── _wiki/
```

---

## 🎮 Basic Usage

### Opening AutoLFM
Type in chat:
```
/lfm
```

This opens the main AutoLFM interface.

### Main Features

#### 1. **Dungeon Selection**
- Click checkboxes to select dungeons you want to run
- Maximum 3 dungeons can be selected simultaneously
- Selected dungeons appear in your broadcast message

#### 2. **Message Customization**
- Click "Message" tab to edit your LFM broadcast
- Preview shows exactly what will be broadcast
- Click "Insert VAR" button to see available variables

#### 3. **Broadcasting**
- Click "Broadcast" button to send your LFM message to chat
- Use "Auto-Broadcast" for automatic periodic broadcasting
- Set broadcast frequency in settings

#### 4. **Auto-Invite**
- Configure auto-invite filters in the "Auto-Invite" tab
- Set class requirements, level requirements
- Automatically accept invites matching your criteria

### Debug Mode

For advanced users and developers:
```
/lfm debug
```

Shows internal component registry and state information.

---

## ⚙️ Configuration

### Settings Tab
- **Auto-Broadcast Interval**: How often to re-broadcast your message
- **Auto-Invite Filters**: Customize who gets auto-invited
- **Color Preferences**: Set custom UI colors

### Saving Configuration
All settings are automatically saved to your WoW SavedVariables.

---

## 🆘 Troubleshooting

### AutoLFM doesn't appear
- Verify addon is enabled in AddOns list
- Check addon folder is named exactly "AutoLFM"
- Restart WoW completely

### Broadcast not sending
- Verify dungeon is selected
- Check message is not empty
- Ensure you're not in restricted channels

### Auto-Invite not working
- Check filters are configured correctly
- Verify you're accepting invites
- Check chat log for any error messages

---

## 📚 Documentation

### For Users
Start here: This guide covers installation, basic usage, configuration, and troubleshooting.

### For Developers

**Quick Start:**
1. [Developer-Guide.md](Developer-Guide.md) - Developer overview and quick reference
2. [Maestro-Architecture.md](Maestro-Architecture.md) - Understand the CQRS command bus system
3. [Best-Practices.md](Best-Practices.md) - Lua 5.0 compatibility and coding standards
4. [ID-System-Reference.md](ID-System-Reference.md) - Registry, IDs, state management, and component patterns
5. [API.md](API.md) - Public API documentation for external addons

**Project Structure:**
```
AutoLFM/
├── README.md                          (Project overview)
├── _wiki/
│   ├── Home.md                        (Documentation hub)
│   ├── Installation-Usage.md          (This file - user guide)
│   ├── Developer-Guide.md             (Developer entry point)
│   ├── Maestro-Architecture.md
│   ├── Best-Practices.md
│   ├── ID-System-Reference.md         (Registry, IDs, state management)
│   └── API.md
├── Core/                              (Framework)
├── Components/                        (Reusable components)
├── Logic/                             (Business logic)
└── UI/                                (User interface)
```

---

## 📞 Support

Found a bug or have suggestions? Please report at:
[GitHub Issues - AutoLFM](https://github.com/FSuhas/AutoLFM/issues)

For development questions, see [Developer-Guide.md](Developer-Guide.md)
