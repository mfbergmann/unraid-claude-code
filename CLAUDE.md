# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Unraid plugin that installs Claude Code CLI on Unraid servers with persistence across reboots. The entire plugin is defined in a single file: `claude-code.plg`.

## Architecture

The plugin file (`claude-code.plg`) is an XML document using DOCTYPE entity declarations for maintainability. It contains:

- **Entity declarations**: Plugin name, author, version, URLs — referenced throughout via `&name;`, `&plugdir;`, etc.
- **Plugin metadata**: Version, author, pluginURL, support URL, icon, minimum Unraid version
- **CHANGES section**: Release notes (date-based versioning `YYYY.MM.DD`)
- **FILE elements**: Embedded scripts and configuration files

### Key components within the plugin:

- **Install script** (`scripts/install.sh`): Installs Claude Code via the official native installer, caches the binary on the array, sets up symlinks for persistent config/auth
- **Web UI** (`claude-code.page`): PHP settings page at Settings → Utilities → Claude Code
- **Startup script** (`rc.claude-code`): rc.d script that waits for network, then runs install in the background
- **Config file** (`claude-code.cfg`): Stores the appdata path on the flash drive
- **Removal script**: Cleans up binaries, symlinks, and go file entries (preserves user data)

### File locations at runtime:

- `/boot/config/plugins/claude-code/` — Persistent config on flash drive
- `/usr/local/emhttp/plugins/claude-code/` — Plugin runtime files (RAM disk)
- `/etc/rc.d/rc.claude-code` — Boot startup script
- `$APPDATA_PATH/` — Cached binary + persistent auth on array

### Persistence strategy:

1. Claude binary is cached on the array (appdata) — restored on boot without re-downloading
2. `~/.claude` is symlinked to appdata — auth tokens survive reboots
3. `~/.claude.json` is symlinked to appdata — settings survive reboots
4. rc.d script in go file ensures setup runs on every boot

## Community Applications

The `claude-code.xml` file is the Community Applications template. It must reference the raw GitHub URL of the `.plg` file on the `main` branch.

## Development Workflow

Serve the plugin locally for testing:
```bash
python3 -m http.server 8080
```

On Unraid terminal:
```bash
# Install from dev machine
plugin install http://YOUR_DEV_IP:8080/claude-code.plg

# Reinstall after changes
plugin remove claude-code.plg && plugin install http://YOUR_DEV_IP:8080/claude-code.plg
```

## Releasing

Uses date-based versioning (`YYYY.MM.DD`) per Unraid plugin conventions:
```bash
bump-my-version replace --new-version 2025.12.01
git push && git push --tags
```

The `.bumpversion.toml` config updates three version locations in `claude-code.plg` automatically:
- Entity version declaration
- CHANGES section header
- Post-install message
