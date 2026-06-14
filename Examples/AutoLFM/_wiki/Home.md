# AutoLFM Documentation

Welcome to the complete AutoLFM documentation. This folder contains guides for both users and developers.

---

## 📖 For Users

**[Installation-Usage.md](Installation-Usage.md)**
- Complete installation instructions
- Feature guide and usage
- Troubleshooting and support

---

## 👨‍💻 For Developers

### Quick Start
Start with the [Developer Guide](Developer-Guide.md) for a quick introduction.

### Core Documentation
| Document | Purpose |
|----------|---------|
| [Maestro-Architecture.md](Maestro-Architecture.md) | Maestro command bus architecture and patterns |
| [Best-Practices.md](Best-Practices.md) | Lua 5.0 coding standards and conventions |
| [ID-System-Reference.md](ID-System-Reference.md) | **Registry & IDs** - All 92 Maestro IDs, state management, and component organization |
| [API.md](API.md) | Public API for external addon integration |

---

## 🚀 Where to Start?

### If you're a **new developer**:
1. [Developer-Guide.md](Developer-Guide.md) - Overview of the system
2. [Maestro-Architecture.md](Maestro-Architecture.md) - How the system works
3. [Best-Practices.md](Best-Practices.md) - How to write code
4. [ID-System-Reference.md](ID-System-Reference.md) - Component management and state patterns

### If you're an **experienced developer**:
- [ID-System-Reference.md](ID-System-Reference.md) - Complete ID lookup and state management patterns
- [API.md](API.md) - Public API documentation

### If you want to **add a new feature**:
1. [ID-System-Reference.md](ID-System-Reference.md) - Find available IDs
2. [Maestro-Architecture.md](Maestro-Architecture.md) - Understand patterns
3. [Best-Practices.md](Best-Practices.md) - Follow conventions
4. [ID-System-Reference.md](ID-System-Reference.md#adding-new-components) - Register your component and follow state patterns

### If you want to **integrate with the API**:
- [API.md](API.md) - Public API reference
- [Developer-Guide.md](Developer-Guide.md#essential-rules) - Important rules

---

## 📊 System Overview

### The 5 ID Categories
```
Commands:       C01 - C24  (24 commands, all used)
Events:         E01 - E09  (9 events, all used)
Listeners:      L01 - L12  (12 listeners, all used)
States:         S01 - S20  (20 states, all used)
Init Handlers:  I01 - I27  (27 handlers: 24 explicit + 3 auto-assigned)

TOTAL: 92 unique IDs
```

### Core Data Flow
```
User Action → Command → State Change → Event → UI Update
     (C##)      Handler       (S##)      (E##)   (L##)
```

---

## ✅ Verification Status

**All 92 Maestro registry IDs have been verified and are:**
- ✅ Correctly implemented in source code
- ✅ Consistently documented
- ✅ Organized by functional domain
- ✅ Free of gaps, duplicates, or conflicts
- ✅ All code examples use real, existing IDs

---

## 📁 Documentation Structure

```
_wiki/
├── Home.md (this file - documentation hub)
├── Installation-Usage.md (user guide)
├── Developer-Guide.md (developer guide & quick start)
├── Maestro-Architecture.md (system design & patterns)
├── Best-Practices.md (Lua 5.0 standards & conventions)
├── ID-System-Reference.md (registry, IDs, state management)
└── API.md (public addon integration API)
```

---

## 🎯 Key Principles

1. **IDs are immutable** - Never change an ID after it's assigned
2. **Commands are the only way to modify state** - No direct state access
3. **Events notify the system** - UI and logic react to events
4. **States are the single source of truth** - No data duplication
5. **Listeners react to changes** - UI stays synchronized with state

---

## 📝 Adding New Components

When adding new commands, events, states, listeners, or init handlers:

1. **Choose the next available ID** - Check [ID-System-Reference.md](ID-System-Reference.md) for the current max in your category
2. **Register with that ID:**
   ```lua
   RegisterCommand("MyFeature.Action", handler, { id = "C25" })
   ```
3. **Update [ID-System-Reference.md](ID-System-Reference.md)** - Add entry in appropriate section and update state management patterns if needed

For detailed instructions, see [ID-System-Reference.md#adding-new-components](ID-System-Reference.md#adding-new-components)

---

## ❓ FAQ

**Q: Where do I find all 92 IDs?**
→ [ID-System-Reference.md](ID-System-Reference.md) - Complete inventory, state management, and component organization

**Q: What's the next available ID for a new command?**
→ Currently C24 is the last used, so C25 would be next. Check [ID-System-Reference.md](ID-System-Reference.md) for current max in each category

**Q: Can I reuse a deleted ID?**
→ No, IDs are immutable. Deleted components leave gaps, which is fine.

**Q: How do I know which domain a component belongs to?**
→ See [Maestro-Architecture.md](Maestro-Architecture.md) for domain patterns

**Q: Where can I see the public API?**
→ [API.md](API.md) - Complete external addon integration documentation

**Q: Are all IDs correct everywhere?**
→ Yes - ✅ All 92 IDs verified in source code and documentation

---

## 🔗 Quick Links

- **[Developer Guide](Developer-Guide.md)** - Start here
- **[Registry & IDs](ID-System-Reference.md)** - All 92 IDs and state management
- **[Architecture](Maestro-Architecture.md)** - System design
- **[Best Practices](Best-Practices.md)** - Coding standards
- **[Public API](API.md)** - External integration