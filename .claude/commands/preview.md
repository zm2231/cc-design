---
name: cc-design:preview
description: Preview Stitch screens inline — opens screenshots or saves to .cc-design/previews/.
---

# cc-design preview

Preview Stitch screens without leaving Claude Code.

## Usage

```
/cc-design:preview                    # Preview all screens in .cc-design/stitch.json
/cc-design:preview <screen-id>        # Preview a specific screen
/cc-design:preview --save             # Save all previews to .cc-design/previews/ instead of opening
```

## Flow

1. Read `.cc-design/stitch.json` — get screen IDs
2. For each screen (or specified screen): call `get_screen` to get screenshot URL
3. **Open in browser** (default): run `open <screenshot-url>` — opens directly in default browser
4. **Save locally** (if `--save` or no display): fetch screenshot URL → save to `.cc-design/previews/[screen-id].png`

## Implementation

1. Check for `.cc-design/stitch.json` — if missing, say "No Stitch screens found. Run /cc-design:extract <url> first."
2. If a specific screen ID is passed, call `get_screen { screenId }` — use `screenshotUri` from response
3. If no screen ID, list all screens from stitch.json with descriptions, then open each
4. Open with: `open "[screenshotUri]"` (macOS) — this opens the Google-hosted image in browser
5. If `--save` flag: fetch the URL with WebFetch (binary), write to `.cc-design/previews/[id].png`
6. After opening, show a summary:

```
Opened 3 screens:
  • Variant A (hero + grid)      → d6d7d4...
  • Dark inversion               → 9cbbbb...
  • Blueprint intake             → a1a74d...

To save locally: /cc-design:preview --save
To iterate on a screen: /cc-design:extract --stitch <project-id>
```

## If Stitch MCP is not connected

```
Stitch MCP not connected — can't fetch screen previews.

Screens recorded in .cc-design/stitch.json:
  • [screen descriptions from stitch.json]

Connect Stitch and run /cc-design:preview again.
```
