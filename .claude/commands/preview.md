---
name: cc-design:preview
description: Preview Stitch screens by downloading HTML locally and opening in browser.
---

# cc-design preview

Preview Stitch screens without leaving Claude Code.

## Usage

```
/cc-design:preview                    # Preview all screens in .cc-design/stitch.json
/cc-design:preview <screen-id>        # Preview a specific screen
```

## How It Works

Stitch URLs require Google auth — screenshot URLs 403 in browser, the local `stitch-mcp serve` proxy times out on asset fetches. The working approach:

1. Call `get_screen` via MCP to get `htmlCode.downloadUrl`
2. Fetch HTML with `x-goog-api-key: $STITCH_API_KEY` header — returns 200
3. Save to `/tmp/stitch-preview-[screenId].html`
4. Open local file in browser

## Implementation

1. Check for `.cc-design/stitch.json` — if missing, say "No Stitch project found. Run `/cc-design:extract <url>` first."
2. Read `projectId` from `.cc-design/stitch.json`. If a screen ID was passed, process only that screen; otherwise use all screens listed in `stitch.json`.
3. For each screen, call `get_screen` to get `htmlCode.downloadUrl`
4. Run:
   ```bash
   curl -s -o /tmp/stitch-preview-[screenId].html \
     -H "x-goog-api-key: $STITCH_API_KEY" \
     "[downloadUrl]"
   ```
5. Run: `open /tmp/stitch-preview-[screenId].html`
6. Show summary:

```
Opened 3 screens:
  • Main Landing Page          →  /tmp/stitch-preview-d6d7d4d9.html
  • Dark Variant C             →  /tmp/stitch-preview-9cbbbb9f.html
  • Get Your Blueprint         →  /tmp/stitch-preview-a1a74df9.html
```

## If Stitch MCP is not connected

Tell the user Stitch isn't connected and refer them to `references/stitch-setup.md`.
