# Claude Code for Unraid

An Unraid plugin that makes [Claude Code](https://github.com/anthropics/claude-code) — Anthropic's AI coding assistant — persistent on your server. No more reinstalling after every reboot.

## The Problem

Unraid runs from RAM. Anything you install via SSH (including Claude Code) disappears on reboot. This plugin solves that by:

- Caching the Claude binary on your array
- Symlinking auth tokens and config to your appdata directory
- Automatically restoring everything on boot via an rc.d startup script

## Quick Start

**Install the plugin:**

```bash
plugin install https://raw.githubusercontent.com/mfbergmann/unraid-claude-code/main/claude-code.plg
```

**Open the Unraid terminal and run:**

```bash
claude
```

Authenticate once. It sticks across reboots.

## What Gets Persisted

| Item | Location | Purpose |
|------|----------|---------|
| Claude binary | `$APPDATA_PATH/bin/claude` | Restored from cache on boot (no download needed) |
| `~/.claude/` | Symlinked to appdata | Auth tokens, session data |
| `~/.claude.json` | Symlinked to appdata | User settings and preferences |

The appdata path is auto-detected from your Docker config, or you can set it manually via **Settings > Utilities > Claude Code**.

## Requirements

- Unraid 6.12.0+
- Array started (for appdata persistence)
- Internet connection (first install only)

## Web UI

The plugin adds a settings page at **Settings > Utilities > Claude Code** where you can:

- View installation status, persistence state, and auth status
- Configure the appdata storage path
- Reinstall or update Claude Code
- View the install log

## Troubleshooting

**Check the install log:**

```bash
cat /var/log/claude-code-install.log
```

**Re-run the installer manually:**

```bash
/usr/local/emhttp/plugins/claude-code/scripts/install.sh
```

**Claude not found after boot?** The rc.d script runs in the background — it may take a minute to restore the binary while waiting for the array and network. Check the log to see progress.

## Development

```bash
# Serve the plugin from your dev machine
python3 -m http.server 8080

# On the Unraid terminal
plugin install http://YOUR_DEV_IP:8080/claude-code.plg

# Reinstall after changes
plugin remove claude-code.plg && plugin install http://YOUR_DEV_IP:8080/claude-code.plg
```

### Versioning

Date-based (`YYYY.MM.DD`) per Unraid plugin conventions:

```bash
bump-my-version replace --new-version 2026.04.04
git push && git push --tags
```

## License

MIT
