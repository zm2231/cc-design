# Stitch Setup

Stitch resolves semantic color tokens (`surface`, `on_surface`, `primary_container`, etc.) — the role-based layer on top of a raw palette. Without it, extract still works for spacing, typography, radius, and structure.

## How to connect

1. Get an API key at [stitch.withgoogle.com/settings](https://stitch.withgoogle.com/settings) → API Keys → Generate
2. Add to `~/.zshrc`:
   ```bash
   export STITCH_API_KEY=your-key-here
   ```
3. Restart Claude Code — the plugin connects automatically.
