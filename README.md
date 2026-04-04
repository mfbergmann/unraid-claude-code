# Claude Code Unraid Plugin

![Claude Code Unraid Plugin](assets/banner.jpg)

Installs [Claude Code](https://github.com/anthropics/claude-code) CLI on Unraid with full persistence across reboots.

## Features

- Installs Claude Code CLI via the official Anthropic installer
- Binary cached on the array — no re-download needed after reboot
- Authentication and settings persist automatically via symlinks to appdata
- Web UI settings page under **Settings > Utilities > Claude Code**
- Automatic setup on boot via rc.d startup script

## Install

```bash
plugin install https://raw.githubusercontent.com/mfbergmann/unraid-claude-code/main/claude-code.plg
```

## Usage

Open the Unraid terminal and run:

```bash
claude
```

On first run, follow the authentication prompts. Your credentials persist automatically.

Configure the appdata path via **Settings > Utilities > Claude Code**.

## Requirements

- Unraid 6.12.0+
- Array started (for persistence)
- Internet connection (first install only)

## How It Works

1. **Binary caching**: The Claude binary is cached at `$APPDATA_PATH/bin/claude` on your array. On reboot, it restores from cache instantly without needing network access.
2. **Config persistence**: `~/.claude` and `~/.claude.json` are symlinked to your appdata directory, so auth tokens and settings survive reboots.
3. **Boot startup**: An rc.d script runs in the background on boot, waits for network/array, then restores everything.

## Troubleshooting

Check the install log:

```bash
cat /var/log/claude-code-install.log
```

Manually re-run the installer:

```bash
/usr/local/emhttp/plugins/claude-code/scripts/install.sh
```

## Development

```bash
# Serve plugin locally
cd /path/to/unraid-claude-code
python3 -m http.server 8080

# On Unraid terminal - install
plugin install http://YOUR_DEV_IP:8080/claude-code.plg

# Reinstall after changes
plugin remove claude-code.plg && plugin install http://YOUR_DEV_IP:8080/claude-code.plg
```

### Releasing

Uses date-based versioning (`YYYY.MM.DD`) per Unraid plugin conventions:

```bash
bump-my-version replace --new-version 2025.12.01
git push && git push --tags
```
