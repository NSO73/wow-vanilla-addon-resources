# Examples

Complete, real-world addons kept here as study material. While the rest of this
repository is *reference* (API docs, Lua 5.0 notes, extracted game assets), this
folder shows those concepts assembled into working addons you can read end to end.

## AutoLFM

[`AutoLFM/`](AutoLFM/) — Automated "Looking For More" broadcaster for Turtle WoW
(WoW 1.12 / Interface 11200).

An **archived snapshot** of a finished, polished addon. Turtle WoW has closed and
the addon is no longer maintained, so it is preserved here as a reference example.

It is a good end-to-end illustration of common addon patterns:

- **File separation** — code split across `Core/`, `Components/`, `Logic/`, `UI/`
  and wired together through the `.toc` load order.
- **Saved variables** — per-character settings and presets persisted via
  `SavedVariables` (see `Core/Storage.lua`).
- **XML templates** — UI built from reusable `<Frame>`/`<Button>` templates in
  `UI/Templates/*.xml`, instantiated and driven from Lua.
- **Command-bus architecture** — a small CQRS-style "Maestro" command bus
  (`Core/Maestro.lua`) decoupling commands, events, states and listeners.
- **Assets** — BLP textures, OGG sounds and a minimap button, showing how custom
  art is packaged and referenced.

Start with the addon's own developer documentation in
[`AutoLFM/_wiki/`](AutoLFM/_wiki/):

- [Developer-Guide.md](AutoLFM/_wiki/Developer-Guide.md) — overview and quick start
- [Maestro-Architecture.md](AutoLFM/_wiki/Maestro-Architecture.md) — the command bus
- [Best-Practices.md](AutoLFM/_wiki/Best-Practices.md) — Lua 5.0 coding standards
- [ID-System-Reference.md](AutoLFM/_wiki/ID-System-Reference.md) — registry & state
- [API.md](AutoLFM/_wiki/API.md) — public integration API
