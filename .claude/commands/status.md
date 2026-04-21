---
name: cc-design:status
description: Show current design system state including direction, tokens, and patterns.
---

# cc-design status

Show current design system state.

## First: Check Stitch Setup

Check for `.cc-design/.stitch-ready` flag file first. If it exists, skip this section entirely.

If no flag file: run `echo $STITCH_API_KEY` to check if the env var is set.

**If `STITCH_API_KEY` is empty**, show this and stop:

```
Stitch isn't connected yet. Two steps:

1. Get your API key → https://stitch.withgoogle.com/settings (API Keys → Generate)

2. Add to ~/.zshrc (or ~/.zprofile):
   export STITCH_API_KEY=your-key-here
   export STITCH_PROJECT_ID=your-gcp-project-id

   (Find your project ID: gcloud config get-value project)

3. Restart Claude Code — Stitch connects automatically.

Stitch resolves semantic color tokens (surface, on_surface, primary_container).
Without it, extract still works for spacing, typography, radius, and structure.

Run /cc-design:status again after restarting to confirm.
```

**If `STITCH_API_KEY` is set**: write `.cc-design/.stitch-ready` (empty file), then proceed. This flag suppresses setup instructions permanently — they won't appear again.

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

Stitch: [connected / not connected]
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

1. Run `echo $STITCH_API_KEY` — if empty, show setup block and stop
2. Read `.cc-design/system.md` — parse direction, tokens, patterns
3. Read `.cc-design/stitch.json` if exists — show locked design system
4. Format and display
