---
name: cc-design:extract
description: Extract design patterns from existing code, a live URL, or a Stitch project to create a system.md file.
---

# cc-design extract

Extract design tokens and patterns to bootstrap or update `.cc-design/system.md`.

## Usage

```
/cc-design:extract                        # Scan common UI paths in current project
/cc-design:extract <path>                 # Scan a specific local directory
/cc-design:extract <url>                  # Extract from a live page (+ Stitch resolution if available)
/cc-design:extract --stitch <project-id>  # Extract from an existing Stitch project
```

## First: Check for Existing State

Before extracting, check:

1. Does `.cc-design/system.md` exist? If so, offer to **merge** rather than overwrite — preserving existing decisions.
2. Does `.cc-design/stitch.json` exist? If so, read it. It contains the locked design system ID. Every Stitch call in this session must use that asset ID — do not create a new design system.

```json
// .cc-design/stitch.json shape
{
  "projectId": "...",
  "designSystemId": "...",    // Lock — use this in all generate_screen_from_text calls
  "designSystemName": "...",
  "screens": [
    { "id": "...", "description": "...", "createdAt": "..." }
  ],
  "fontMappings": {
    "Instrument Serif": "SOURCE_SERIF_FOUR",
    "Inter": "INTER"
  }
}
```

If `stitch.json` exists, append to every `generate_screen_from_text` brief:
> "Use existing design system `assets/[designSystemId]`. Do not create a new one. The URL/reference is visual reference only."

To override and create a new design system, pass `--new-system`.

---

## Combined Flow (Default for URL)

Modern frontends (Tailwind v4, CSS-in-JS, runtime theming) don't expose semantic tokens in static HTML. A URL scan alone finds palette colors but misses role assignments like "what color is a primary button?".

**The combined flow:**

1. **Phase 1 — Static scan**: Fetch the URL, extract what's available
2. **Phase 2 — Stitch resolution** *(if Stitch MCP available)*: Use Phase 1 findings as brief, call `generate_screen_from_text`, extract `namedColors` — semantically resolved tokens
3. **Merge**: Combine Phase 1 structural data with Phase 2 semantic colors into system.md

**If Stitch MCP is not available**, Phase 1 results are used directly.

---

## Phase 1: Static Scan

### Source: Live URL

1. Use WebFetch to retrieve the page HTML
2. **CSS priority order** (WebFetch summarizes HTML — go deeper):
   - Extract `<link rel="stylesheet">` hrefs, fetch each CSS file directly
   - Parse `<style>` blocks for CSS custom properties (`--*`)
   - Scan Tailwind class strings in HTML for color/spacing/radius patterns
   - Check for `designTokens.json`, `tokens.json` at common paths (`/tokens.json`, `/_next/static/...`)
3. Parse CSS for:
   - `oklch(...)`, `hsl(...)`, `rgb(...)`, `#hex` color values — all formats
   - CSS custom properties: `--color-*`, `--spacing-*`, `--radius-*`
   - Tailwind config hints (look for `--tw-*` variables)
4. Identify typography scale from `font-size`, `font-family` usage
5. Infer depth from box-shadow vs border frequency

**Confidence reporting** — for every token found, state how it was discovered:

```
Fonts: Instrument Serif (found directly in @font-face), Inter (found in font-family declarations)
Primary color: #4A7BF7 (found directly in --color-primary), oklch(0.985 0.002 90) → approx #FAF9F7 (converted from oklch)
Spacing: 16px (inferred — 31 class usages of p-4), 8px (inferred — 23 usages of p-2)
Dark/light mode: Light-mode first (inferred from bg-background on body — verify with screenshot)
```

**oklch colors:** Convert to hex for system.md and Stitch compatibility. Note the original value.

### Source: Local Code

Scan UI files: `tsx, jsx, vue, svelte, css, scss, tokens.json, *.tokens.json, designTokens.json`

1. **Repeated spacing values**
   ```
   Found: 4px (12x), 8px (23x), 16px (31x), 24px (8x)
   → Suggests: Base 4px, Scale: 4, 8, 16, 24
   ```
2. **Repeated radius values**
3. **Button patterns** — height, padding, radius
4. **Card patterns** — border, padding, shadow
5. **Depth strategy** — shadow count vs border count
6. **Color values** — hex, rgb, hsl, oklch, CSS variable names

---

## Phase 2: Stitch Resolution

**When Stitch MCP is available** (tools: `generate_screen_from_text`, `get_screen`):

1. Synthesize Phase 1 findings into a brief:
   ```
   "[Product name] [product type: dashboard / landing page / tool]. [Dark/light] theme.
   Primary color: #hex. Fonts: [headline], [body]. Style: [adjectives from visual scan].
   Show: [key UI elements found — hero, nav, cards, charts, CTA, etc.]
   [URL reference if applicable — visual reference only]"
   ```

2. If `stitch.json` exists with a `designSystemId`, append:
   > "Use existing design system `assets/[designSystemId]`. Do not create a new one."

3. Call `generate_screen_from_text`

4. Extract from the response:
   - `namedColors` — fully resolved semantic palette
   - `overridePrimaryColor` — confirmed brand accent
   - `fontFamilies` — headline, body, label fonts
   - `designSystemId` / `designSystemName` — save to `stitch.json`

5. If response includes `htmlCode.downloadUrl`, fetch it for exact component values (radius, padding, border style)

6. **Save to `.cc-design/stitch.json`** — append new screen, record design system ID if new

**If Stitch MCP tools are not available**, show exactly this and stop:
```
Stitch MCP not connected. Add it with:

  claude mcp add stitch \
    -e STITCH_API_KEY=your-key \
    -e STITCH_PROJECT_ID=your-gcp-project-id \
    -- npx @_davideast/stitch-mcp proxy

Get your API key at https://stitch.withgoogle.com/settings
```

---

## Source: Existing Stitch Project (`--stitch <project-id>`)

1. Call `list_screens` with `{ projectId }` to enumerate screens
2. Call `get_screen` for each — parse HTML for tokens
3. Aggregate: tokens in 3+ screens are system-level; note screen-specific ones separately

---

## Output Format

**Always present findings and wait for confirmation before writing:**

```
Extracted from [source]:

Phase 1 (static scan):
  Fonts: Instrument Serif (found directly), Inter (found directly)
  Palette: #4A7BF7 (found directly), oklch(0.985 0.002 90) → #FAF9F7 (converted)
  Spacing: 4, 8, 16, 24 (base 4px, inferred from class usage)
  Radius: 8px base (inferred), 16px lg (inferred)
  Depth: ghost borders, 1px solid rgba (found directly)
  Mode: Light-first (inferred — verify)

Phase 2 (Stitch resolution):
  background: #faf9f7        [semantic — authoritative]
  surface: #ffffff
  surface_container: #f0eeeb
  primary_container: #4A7BF7
  on_surface: #1e1c18
  outline: #c8c4bc
  error: #ba1a1a
  Design system: Cadence AI (assets/16067147807800514904) — saved to stitch.json

Stitch font mappings (actual → Stitch):
  Instrument Serif → Source Serif 4
  Inter → Inter
  JetBrains Mono → Geist Mono

Write .cc-design/system.md? (y/n/customize)
```

Do not write until the user confirms.

---

## system.md Structure

When writing, always include a `## Quick Reference` section at the top:

```markdown
## Quick Reference

| Token | Value |
|-------|-------|
| Background | #faf9f7 |
| Primary text | #1e1c18 |
| Primary CTA | #4A7BF7 bg, #ffffff text, 8px radius |
| Card | white bg, 1px solid rgba(0,0,0,0.08) border, 16px radius |
| Heading font | Instrument Serif |
| Body font | Inter |

---
[full spec below]
```

If a Stitch project was used, include a `## Stitch` section:

```markdown
## Stitch

Project: `projects/[projectId]`
Design system: `assets/[designSystemId]` — **locked, do not create new**

Font mappings (actual → Stitch):
- Instrument Serif → Source Serif 4
- Inter → Inter
- JetBrains Mono → Geist Mono

Screens:
- `[screenId]` — Hero + grid (Variant A)
- `[screenId]` — Dark inversion
```

---

## Implementation

1. Check for `.cc-design/stitch.json` — if present, read and lock design system
2. Check for `.cc-design/system.md` — if present, offer merge
3. Detect source type from arguments
4. **URL**: Fetch HTML → extract linked CSS URLs → fetch CSS files → parse tokens
5. **Local**: Glob for `tsx, jsx, vue, svelte, css, scss, *.tokens.json, designTokens.json`
6. **Phase 2** (if Stitch MCP available): synthesize brief → `generate_screen_from_text` (with locked design system if stitch.json exists) → extract tokens
7. **`--stitch <id>`**: `list_screens` → `get_screen` for each → aggregate
8. Present findings with confidence levels — **wait for confirmation**
9. On confirmation: write/merge `.cc-design/system.md` + update `.cc-design/stitch.json`
