# cc-design

AI defaults to generic. Ask it to build a dashboard and it gives you the same indigo gradient card layout every time. The code works, but the craft is missing. That is the problem cc-design solves.

It is a Claude Code plugin that enforces intent-first design thinking before any code gets written. The AI has to answer who the human is, what they are trying to do, and how it should feel before it touches a single line. Then it captures those decisions in `.cc-design/system.md` so future work compounds instead of resetting.

You get dashboards, admin panels, and tools that feel like they were designed for the specific problem, not copied from a template.

## What it does

**Intent enforcement.** The AI cannot default to generic patterns. Every design choice must be justified with intent, not "this is common."

**Design memory.** Patterns live in `.cc-design/system.md` so future work builds on what you have already established.

**Consistency checks.** Run `/cc-design:audit` to find spacing, depth, color, and pattern violations across your codebase.

**Pattern extraction.** Pull design tokens from existing code, live URLs, or Stitch projects.

## Stitch integration

Google's Stitch is a design system tool that can generate UI from text descriptions. When you connect it via the MCP server, you get something most tools do not: semantically resolved color tokens.

Here is why that matters. When you scan a live URL for design tokens, you get palette colors; hex values without semantic meaning. Stitch resolves these into roles like `surface`, `surface_container`, `on_surface`; the actual design intent, not just the values.

The extraction flow runs in two phases:

1. **Static scan:** Fetch the URL, extract CSS variables, colors, fonts, spacing, Tailwind classes
2. **Stitch resolution:** Use those findings as a brief, call `generate_screen_from_text`, extract the semantic tokens from the response

You end up with a complete `system.md` that has both structural patterns and fully resolved semantic colors. Without Stitch you still get the structural data, but semantic roles stay unresolved.

## Commands

| Command | What it does |
|---------|-------------|
| `/cc-design:init` | Main design workflow. Checks for existing system state, asks intent questions, builds with craft. |
| `/cc-design:extract` | Extract design patterns from code, a URL, or a Stitch project. |
| `/cc-design:status` | Show your current design system state from `.cc-design/system.md`. |
| `/cc-design:audit` | Check code against your design system for violations. |
| `/cc-design:critique` | Critique your build for craft, then rebuild what defaulted. |

## How it works

The core idea is that every choice must be a choice. Before generating any UI, the AI has to articulate:

1. Who is this human?
2. What must they accomplish?
3. How should this feel?

Then it declares intent for the design:

```
Intent: [who, what they need to do, how it should feel]
Palette: [foundation + accent, and WHY these colors fit this product's world]
Depth: [borders / subtle shadows / layered, and WHY]
Surfaces: [elevation scale, and WHY this temperature]
Typography: [typeface choice, and WHY it fits the intent]
Spacing: [base unit]
```

After the build, it offers to save these patterns to `.cc-design/system.md`. Next time, those patterns load automatically. Your design system compounds instead of resetting.

## Installation

### From the Claude Code marketplace

Add the marketplace, then install:

```
/plugin marketplace add zm2231/cc-design
```

Select cc-design from the `/plugin` menu and restart Claude Code.

### Manual install

Clone into your Claude Code plugins directory:

```bash
cd ~/.claude/plugins
git clone https://github.com/zm2231/cc-design.git
```

The `.claude-plugin/plugin.json` registers the commands, skill, and Stitch MCP server automatically.

### Stitch setup (optional)

For semantic color token resolution:

1. Go to [stitch.withgoogle.com/settings](https://stitch.withgoogle.com/settings) and generate an API key
2. Register the MCP with Claude Code:

```bash
claude mcp add stitch \
  -e STITCH_API_KEY=your-api-key \
  -e STITCH_PROJECT_ID=your-gcp-project-id \
  -- npx @_davideast/stitch-mcp proxy
```

3. Restart Claude Code

To find your GCP project ID: `gcloud config get-value project`

Without Stitch, the plugin still works for everything except semantic color resolution. You get structural patterns, spacing, typography, and depth; just not the `surface`/`on_surface`/`primary_container` token roles.

## Not for

Marketing sites, landing pages, or promotional design. This is for interface design; dashboards, admin panels, SaaS apps, tools. Things that get used, not just looked at.

## Philosophy

The plugin is built on a few rules:

- **Intent first:** You cannot design without knowing who it is for and what they need to do.
- **Every choice must be a choice:** If you cannot explain WHY, you have not designed.
- **Sameness is failure:** Generic output is rejected. The interface must emerge from THIS user, THIS problem, THIS intent.
- **Subtle layering:** Surfaces stack with barely visible differences. Borders disappear when you are not looking for them.
- **Memory compounds:** Capture decisions so future work builds on what you have already established.

The result is UI that feels like it was built for the specific problem, not generated from a template.

## Project structure

```
.claude-plugin/
  plugin.json           # Plugin manifest for Claude Code
  marketplace.json      # Marketplace listing metadata
.claude/
  commands/
    init.md             # /cc-design:init
    extract.md          # /cc-design:extract
    status.md           # /cc-design:status
    audit.md            # /cc-design:audit
    critique.md         # /cc-design:critique
  skills/
    cc-design/
      SKILL.md          # Core skill: principles, craft knowledge, checks
      references/
        principles.md   # Code examples, specific values, dark mode
        validation.md   # Memory management, when to update system.md
        critique.md     # Post-build craft critique protocol
        example.md      # Example system.md output
reference/
  system-template.md    # Template for .cc-design/system.md
  examples/
    system-cadence.md   # Example: cadence design direction
    system-precision.md # Example: precision design direction
    system-warmth.md    # Example: warmth design direction
.mcp.json               # Stitch MCP server configuration
```
