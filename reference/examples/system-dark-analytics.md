# Design System — Dark Analytics Dashboard

Example system for data-heavy tools and admin interfaces. Dark mode, data-forward, ghost borders only.

## Direction

Dark analytics dashboard. Deep navy backgrounds with indigo-violet primary. Minimal chrome — ghost borders only, no shadows. Tight radius (almost flat). Data-forward: charts, tables, and metrics are the UI.

**Fonts**: Manrope (headlines, labels) · Inter (body, data)
**Depth**: Ghost borders only — `1px solid rgba(70, 69, 85, 0.2)`. No box-shadow.
**Mode**: Dark only.

---

## Colors

### Semantic Palette (Stitch-resolved)

```
background:          #0b1323   Page background
surface:             #0b1323   Card/panel background (same as bg — separation via borders)
surface_container:   #182030   Elevated surface, sidebar, modal
surface_variant:     #1e2a3d   Hover states, subtle distinctions

primary:             #625fff   Brand accent — indigo-violet
primary_container:   #625fff   Filled buttons, active nav, chart highlights
on_primary:          #ffffff   Text/icons on primary
on_primary_dim:      #c2c1ff   Softer primary for secondary actions, links

on_surface:          #dbe2f9   Primary text
on_surface_muted:    #908fa1   Secondary text, placeholders, captions
outline:             #908fa1   Borders, dividers

tertiary:            #ffb68c   Warm accent — alerts, warnings, usage indicators
error:               #ffb4ab   Error state
```

### Palette (raw, for reference)

```
--indigo-500: #625fff
--indigo-300: #c2c1ff
--navy-950:   #0b1323
--navy-900:   #182030
--navy-800:   #1e2a3d
--peach-300:  #ffb68c
--red-300:    #ffb4ab
--slate-400:  #908fa1
--white-soft: #dbe2f9
```

---

## Typography

```
Headline:  Manrope, 600–700 weight
           Sizes: 28px / 22px / 18px

Label:     Manrope, 500–600 weight
           Sizes: 14px / 12px

Body:      Inter, 400 weight
           Size: 14px, line-height 1.5

Data:      Inter, 500–600 weight (tabular-nums)
           Sizes: 14px / 13px / 12px
```

---

## Spacing

Base: **4px**. Scale: 4 · 8 · 12 · 16 · 24 · 32 · 48

```
xs:   4px   — icon gaps, tight label spacing
sm:   8px   — intra-component gaps
md:   12px  — component padding (compact)
base: 16px  — standard component padding
lg:   24px  — section gaps, card padding
xl:   32px  — page section spacing
2xl:  48px  — major layout gaps
```

---

## Radius

Almost flat. Data-first aesthetic keeps radius tight.

```
none: 0px    — tables, data rows
sm:   2px    — chips, badges, tags
md:   4px    — cards, panels, inputs
lg:   8px    — modals, dropdowns
full: 12px   — pill buttons, avatars
```

---

## Depth

**Borders only. No box-shadow.**

Ghost border pattern:
```css
border: 1px solid rgba(70, 69, 85, 0.2);
```

Elevated surface uses `surface_container` background (`#182030`) rather than shadow.

---

## Components

### Button

```
Primary:   bg primary (#625fff), text white, h 36px, px 16px py 8px, radius 8px
Secondary: bg transparent, border ghost, text on_surface_muted, same size
Ghost:     no border, text on_surface_muted, hover: surface_variant bg
Sizes:     sm (28px h, px 12px), md (36px h, px 16px), lg (40px h, px 20px)
```

### Card / Panel

```
bg: surface (#0b1323)
border: ghost (1px solid rgba(70,69,85,0.2))
padding: 16px (compact) / 24px (standard)
radius: 4px
header: 14px Manrope 600, on_surface
```

### Input

```
bg: surface_container (#182030)
border: ghost → primary on focus
height: 36px
padding: 8px 12px
radius: 4px
text: 14px Inter, on_surface
placeholder: on_surface_muted
```

### Table

```
header: 12px Manrope 600, on_surface_muted, uppercase
row: 14px Inter, on_surface, h 40px
divider: ghost border between rows
hover: surface_variant bg
radius: 0 (flat)
```

### Badge / Chip

```
bg: primary_container at 15% opacity
text: on_primary_dim (#c2c1ff)
padding: 2px 8px
radius: 2px (sm)
font: 12px Manrope 500
```

---

## Do / Don't

**Do**
- Use ghost borders to separate surfaces, not shadows
- Lead with data — charts and tables own the visual weight
- Manrope for anything branded; Inter for data/body
- Indigo (#625fff) for a single primary action per context

**Don't**
- Don't add box-shadow — it conflicts with the flat depth strategy
- Don't use more than 2 accent colors in one view (primary + tertiary max)
- Don't use white text on surface_container without checking contrast
- Don't round cards aggressively — 4px max for panels
