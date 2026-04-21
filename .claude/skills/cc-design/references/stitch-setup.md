# Stitch Setup

Stitch resolves semantic color tokens (`surface`, `on_surface`, `primary_container`, etc.) — the role-based layer on top of a raw palette. Without it, extract still works for spacing, typography, radius, and structure.

## How to connect

1. Get an API key at [stitch.withgoogle.com/settings](https://stitch.withgoogle.com/settings) → API Keys → Generate
2. Add to `~/.zshrc` (or `~/.bashrc`):
   ```bash
   export STITCH_API_KEY=your-key-here
   ```
3. Restart Claude Code — the plugin connects automatically.

If the user is stuck at any step, help them: open the settings URL, walk through finding the key, add the export line to their shell config, and confirm it's working after restart.
