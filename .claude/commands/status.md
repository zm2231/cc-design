---
name: cc-design:status
description: Show current design system state including direction, tokens, and patterns.
---

# cc-design status

Show current design system state.

---

## Design System State

**If `.cc-design/system.md` exists:**

```
Design System: [Project Name]

Direction: [Precision & Density / Warmth / etc]
Foundation: [Cool slate / Warm stone / etc]
Depth: [Borders-only / Subtle shadows / Layered]

Tokens:
- Spacing base: 4px
- Radius scale: 4px, 6px, 8px
- Colors: [count] defined

Patterns:
- Button Primary (36px h, 16px px, 6px radius)
- Card Default (border, 16px pad)
- [other patterns...]

Stitch: [connected / not connected — if not, see references/stitch-setup.md]
[If stitch.json exists]: Project locked → assets/[designSystemId]

Last updated: [from file mtime]
```

**If no system.md:**

```
No design system found.

  1. /cc-design:extract          → pull from existing code
  2. /cc-design:extract <url>    → pull from a live page
  3. /cc-design:extract --stitch → pull from a Stitch project
  4. /cc-design:init             → establish direction as you build
```

## Implementation

1. Read `.cc-design/system.md` — parse direction, tokens, patterns
2. Read `.cc-design/stitch.json` if exists — show locked design system
3. Format and display
