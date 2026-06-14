# AutoLFM - Developer Guide

## 📚 Documentation Overview

This guide provides entry points to AutoLFM's development documentation:

| Document | Purpose |
|----------|----------|
| [**Maestro-Architecture.md**](Maestro-Architecture.md) | Complete guide to the Maestro command bus architecture |
| [**Best-Practices.md**](Best-Practices.md) | Lua 5.0 compatibility and development standards |
| [**ID-System-Reference.md**](ID-System-Reference.md) | **Registry & IDs** - All 92 IDs, state management, and component organization |
| [**API.md**](API.md) | Public API for external addon integration |
| [**Home.md**](Home.md) | Main documentation hub |

## 🚀 Quick Start

### 1. Understanding Maestro
Read [**Maestro-Architecture.md**](Maestro-Architecture.md) to understand the command bus architecture that powers AutoLFM.

### 2. Coding Standards
Read [**Best-Practices.md**](Best-Practices.md) for Lua 5.0 compatibility rules and development guidelines.

### 3. Component Management & State
Read [**ID-System-Reference.md**](ID-System-Reference.md) to understand component organization, state management patterns, and all 92 IDs.

### 4. External Integration
Check [**API.md**](API.md) for the public API documentation to integrate AutoLFM with other addons.

## 🎯 Maestro Quick Reference

### Core Components
- **Commands (C##)**: User actions - `Dispatch("Selection.ToggleDungeon", name)`
- **Events (E##)**: Notifications - `EmitEvent("Selection.Changed")`
- **States (S##)**: Data store - `GetState("Selection.Mode")`
- **Listeners (L##)**: Event handlers - registered in Init Handlers only
- **Init Handlers (I##)**: Module initialization with dependencies

### Data Flow
```
User Action → Command → State Change → Event → UI Update
```

### Essential Rules
1. **All components need unique IDs** (C01, E01, L01, S01, I01...)
2. **Listeners ONLY in Init Handlers** - never at file load
3. **States are single source of truth** - don't duplicate data
4. **Commands are the only way to modify state**
5. **Update ID-System-Reference.md** when adding components

## 🔧 Development Workflow

1. **Plan** - Identify required C/E/L/S/I components
2. **Logic** - Implement business logic and state management
3. **UI** - Create user interface and synchronization
4. **Listeners** - Add event handlers for reactivity
5. **Registry** - Update ID-System-Reference.md with new IDs and state patterns
6. **Test** - Use `/lfm debug` to verify registration

## 📁 Project Structure

```
AutoLFM/
├── Core/           # Framework (Maestro, Ticker, Events, Utils)
├── Components/     # Reusable components (Debug, MinimapButton)
├── Logic/          # Business logic and state management
├── UI/             # User interface handlers and templates
└── _wiki/          # Documentation
    ├── Home.md                # Documentation hub
    ├── Installation-Usage.md  # User guide and troubleshooting
    ├── Developer-Guide.md     # Developer guide & quick start
    ├── Maestro-Architecture.md
    ├── Best-Practices.md
    ├── ID-System-Reference.md
    └── API.md
```

---

**For detailed information, follow the links to the specific documentation files above.**