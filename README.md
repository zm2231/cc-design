# cc-design

Ask an AI to build a dashboard. You get the same indigo gradient card layout it gives everyone else. The code works. The craft doesn't exist.

This isn't about the model being bad at design. It's that without forcing intent up front, the AI pulls from the center of distribution. Everything it produces reads as AI-built because it IS the average of a thousand similar UIs. Same sidebar width. Same metric card with icon-left, number-big, label-small. Same color temperature that fits everything and means nothing.

cc-design is a Claude Code plugin that breaks that pattern by making the AI justify every decision before it touches code.

Before anything gets built, three questions have to be answered: who is this human, what do they need to accomplish, and how should this feel? Every visual choice gets explained against those answers. Not against what's common. Not against what "looks clean." Against intent.

Then the decisions get saved to `.cc-design/system.md`. Future sessions load that file and build on what you established instead of starting over. The design system compounds.

## What it does

**Intent enforcement.** The AI can't default. Every design choice requires a WHY that connects back to the specific user and task.

**Design memory.** Patterns live in `.cc-design/system.md`. Spacing base unit, depth strategy, color palette, component patterns; future work inherits all of it.

**Consistency checks.** Run `/cc-design:audit` to catch spacing violations, depth inconsistencies, colors outside the palette, and pattern drift across your codebase.

**Pattern extraction.** Pull design tokens from existing code, live URLs, or Stitch projects with `/cc-design:extract`.

**Screen preview.** If Stitch is connected, `/cc-design:preview` downloads generated screens and opens them locally.

## Stitch integration

Stitch is Google's design system tool. It generates UI from text and returns semantically resolved color tokens.

Here's why that matters. When you scan a live URL for design tokens, you get palette colors; hex values with no semantic meaning. You don't know if `#1e1c18` is your primary text or an accent or a background. Stitch resolves these into roles: `surface`, `surface_container`, `on_surface`, `primary_container`. The actual design intent, not just the values.

The extraction flow runs in two phases. First, a static scan pulls CSS variables, colors, fonts, spacing, and Tailwind classes from the URL. Second, those findings become a brief passed to `generate_screen_from_text`; the semantic tokens come back from the response.

You end up with a `system.md` that has both structural patterns and fully resolved semantic colors. Without Stitch you still get everything structural. Semantic roles just stay unresolved.

## Commands

| Command | What it does |
|---------|-------------|
| `/cc-design:init` | Main design workflow. Explores the domain, forces intent questions, builds with craft. |
| `/cc-design:extract` | Pull design tokens from code, a URL, or a Stitch project. |
| `/cc-design:status` | Show current design system state from `.cc-design/system.md`. |
| `/cc-design:audit` | Check code against your design system for violations. |
| `/cc-design:critique` | Post-build craft critique. Catch what defaulted, rebuild what didn't land. |
| `/cc-design:preview` | Download Stitch screens and open locally. Requires Stitch connected. |

## How it works

Every choice must be a choice. Before generating any UI, the AI declares:

```
Intent: [who this human is, what they must do, how it should feel]
Palette: [colors, and WHY they fit this product's world]
Depth: [borders / subtle shadows / layered, and WHY]
Surfaces: [elevation scale, and WHY this temperature]
Typography: [typeface, and WHY it fits the intent]
Spacing: [base unit]
```

After the build, patterns get saved to `.cc-design/system.md`. Next session loads that file automatically. Your design system compounds instead of resetting every time you open a new conversation.

## Installation

### From the Claude Code marketplace

```
/plugin marketplace add zm2231/cc-design
```

Select cc-design from `/plugin` and restart Claude Code.

### Manual

```bash
cd ~/.claude/plugins
git clone https://github.com/zm2231/cc-design.git
```

The `plugin.json` registers commands, the skill, and the Stitch MCP server automatically.

### Stitch (optional)

The Stitch MCP ships with the plugin and activates when `STITCH_API_KEY` is set in your shell.

1. Get a key at [stitch.withgoogle.com/settings](https://stitch.withgoogle.com/settings) → API Keys → Generate
2. Add to `~/.zshrc` (or `~/.bashrc`):
   ```bash
   export STITCH_API_KEY=your-api-key
   ```
3. Restart Claude Code.

Skip it and the plugin still works for everything except semantic color resolution. Spacing, typography, radius, and structural patterns all extract fine without it.

## Philosophy

A few rules everything is built on.

**Intent first.** You can't design without knowing who it's for and what they need to do.

**Every choice must be a choice.** If you can't explain WHY, you haven't designed. You've defaulted.

**Sameness is failure.** If another AI given the same prompt would produce the same output; you failed. The interface has to emerge from THIS user, THIS problem, THIS context.

**Subtle layering.** Surfaces stack with barely visible differences. Borders disappear when you're not looking for them. Craft whispers.

**Memory compounds.** Capture decisions so future work builds on what's already established instead of resetting.

## Project structure

```
.claude-plugin/
  plugin.json           # Plugin manifest
  marketplace.json      # Marketplace listing
.claude/
  commands/
    init.md             # /cc-design:init
    extract.md          # /cc-design:extract
    status.md           # /cc-design:status
    audit.md            # /cc-design:audit
    critique.md         # /cc-design:critique
    preview.md          # /cc-design:preview
  skills/
    cc-design/
      SKILL.md          # Core skill: principles, craft knowledge, checks
      references/
        principles.md   # Code examples, specific values, dark mode
        validation.md   # Memory management, when to update system.md
        stitch-setup.md # Stitch connection instructions
        example.md      # Example system.md output
reference/
  system-template.md    # Template for .cc-design/system.md
  examples/
    system-dark-analytics.md
    system-precision.md
    system-warmth.md
```
