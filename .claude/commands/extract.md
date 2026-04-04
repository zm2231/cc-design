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

## Combined Flow (Default for URL)

Modern frontends (Tailwind v4, CSS-in-JS, runtime theming) don't expose semantic tokens in static HTML. A URL scan alone will find palette colors but miss role assignments like "what color is a primary button?".

**The combined flow solves this:**

1. **Phase 1 — Static scan**: Fetch the URL, extract what's available (CSS vars, palette colors, fonts, spacing, Tailwind classes)
2. **Phase 2 — Stitch resolution** *(if Stitch MCP is available)*: Use Phase 1 findings as a brief, call `generate_screen_from_text` targeting the same product, extract `namedColors` from the response — this gives fully-resolved semantic tokens
3. **Merge**: Combine Phase 1 structural data (spacing, radius, components) with Phase 2 semantic colors into a complete system.md

**If Stitch MCP is not available**, Phase 1 results are used directly with a note about unresolved semantic roles.

---

## Phase 1: Static Scan

### Source: Live URL

1. Use WebFetch to retrieve the page HTML/CSS
2. Parse `<style>` blocks and linked stylesheets for CSS custom properties (`--*`)
3. Scan inline styles and class patterns for repeated spacing, radius, color values
4. Identify typography scale from font-size usage
5. Infer depth strategy from box-shadow vs border frequency
6. Note Tailwind class patterns if present (e.g. `p-4`, `rounded-lg`, `text-sm`)

Report what was found and confidence level — "found directly" vs "inferred from usage patterns".

### Source: Local Code

Scan UI files (tsx, jsx, vue, svelte, css, scss) for:

1. **Repeated spacing values**
   ```
   Found: 4px (12x), 8px (23x), 16px (31x), 24px (8x)
   → Suggests: Base 4px, Scale: 4, 8, 16, 24
   ```

2. **Repeated radius values**
   ```
   Found: 6px (28x), 8px (5x)
   → Suggests: Radius scale: 6px, 8px
   ```

3. **Button patterns** — height, padding, radius
4. **Card patterns** — border, padding, shadow
5. **Depth strategy** — shadow count vs border count
6. **Color values** — hex, rgb, hsl, CSS variable names

---

## Phase 2: Stitch Resolution

**When Stitch MCP is available** (tools: `generate_screen_from_text`, `get_screen`):

1. Synthesize Phase 1 findings into a brief:
   ```
   "[Product name] dashboard. Dark/light theme. Primary color: #hex.
   Fonts: [headline font], [body font]. Style: [adjectives from visual scan].
   Show: [key UI elements found on the page — charts, tables, nav, cards, etc.]"
   ```

2. Call `generate_screen_from_text` with the brief + any known `projectId`

3. Extract from the response:
   - `namedColors` — fully resolved semantic palette:
     `background`, `surface`, `surface_container`, `primary_container`,
     `on_surface`, `on_primary_container`, `outline`, `error`, etc.
   - `overridePrimaryColor` — confirmed brand accent
   - `overrideNeutralColor` — neutral base
   - `fontFamilies` — headline, body, label fonts
   - `designSystemName` — useful for naming the system.md

4. If the response includes an HTML download URL (`htmlCode.downloadUrl`), fetch it to extract:
   - Exact border-radius values used in components
   - Component height/padding patterns
   - Ghost border patterns (e.g. `1px solid rgba(..., 0.2)`)

**Tokens in the Stitch response are semantically resolved** — they represent actual design intent, not just palette values. Use these as the authoritative color tokens.

**If Stitch MCP tools are not available**, show exactly this and stop:
```
Stitch MCP not connected. The cc-design plugin includes stitch-mcp — try /reload-plugins.

If the Stitch API is not enabled on your Google Cloud project:
  https://console.developers.google.com/apis/api/stitch.googleapis.com/overview
```

---

## Source: Existing Stitch Project (`--stitch <project-id>`)

When a `--stitch <project-id>` flag is passed instead of a URL:

1. Call `list_screens` with `{ projectId }` to enumerate all screens
2. For each screen, call `get_screen` to retrieve its HTML/CSS content
3. Parse each screen's HTML for design tokens (same logic as URL source):
   - CSS custom properties (`--*`)
   - Repeated spacing, radius, color values
   - Typography patterns
   - Component patterns (buttons, cards, inputs)
4. Aggregate across screens — tokens in 3+ screens are system-level patterns; note screen-specific ones separately

---

## Output Format

After extraction, always present findings before writing:

```
Extracted patterns from [source]:

Phase 1 (static scan):
  Fonts: Manrope (headline), Inter (body)
  Palette hint: #625fff (found in 3 CSS rules)
  Spacing: 4, 8, 12, 16, 24 (base 4px)
  Radius: 2px base, 4px md, 8px lg

Phase 2 (Stitch resolution):
  background: #0b1323        [semantic — authoritative]
  surface: #0b1323
  surface_container: #182030
  primary_container: #625fff
  on_surface: #dbe2f9
  outline: #908fa1
  error: #ffb4ab

Depth: Ghost borders (1px solid rgba(70,69,85,0.2))

Create .cc-design/system.md with these? (y/n/customize)
```

If a system.md already exists, offer to **merge** rather than overwrite — preserving existing decisions and adding new ones.

---

## Implementation

1. Detect source type from arguments
2. **URL**: WebFetch → parse CSS/styles/Tailwind classes from HTML and `<style>` blocks
3. **Local**: Glob for UI files → parse for repeated values and patterns
4. **Phase 2** (if Stitch MCP available and source is URL/local): synthesize brief → `generate_screen_from_text` → extract `namedColors` + fonts
5. **`--stitch <id>`**: `list_screens` → `get_screen` for each → aggregate tokens
6. Present findings (Phase 1 + Phase 2) and confirm before writing
7. Write or merge `.cc-design/system.md`
