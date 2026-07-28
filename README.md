# Strata CLI

Cinematic motion design from compact scene JSON — compiled to Idomoo's IDM/VASCO
format and rendered to MP4 via the Idomoo API. Ships as a standalone binary
(no Node/npm needed) with an agent skill for Claude Code, Codex, Cursor and
Antigravity.

This repository hosts the **releases and installers**.

## Install

**Linux / macOS**

```bash
curl -fsSL https://raw.githubusercontent.com/Idomoo-RnD/strata-cli/main/install.sh | bash
```

**Windows (PowerShell)**

```powershell
irm https://raw.githubusercontent.com/Idomoo-RnD/strata-cli/main/install.ps1 | iex
```

The installer downloads the platform binary from
[releases](https://github.com/Idomoo-RnD/strata-cli/releases), verifies its
checksum, installs it (`~/.local/bin/strata` on Unix,
`%LOCALAPPDATA%\Programs\strata\strata.exe` on Windows), and asks which agent
skills to install. Verify with:

```bash
strata version
```

Update anytime with `strata update` (self-replaces from the latest release
here). Prefer manual? Grab a binary from the
[releases page](https://github.com/Idomoo-RnD/strata-cli/releases/latest) and
run it by path.

## Quick start

```bash
strata init scene.json        # starter scene
strata compile scene.json     # compile to a binary .idm locally
strata preview scene.json --at 1 --grid   # free local wireframe preview
strata auth login             # add Idomoo credentials to render MP4s
strata render scene.json      # compile + upload + render an MP4
```

## Agent skill

`strata skill install` puts the `strata-cli` skill into your agent's skill
directory (Claude Code, OpenAI Codex, Cursor, Antigravity IDE/CLI, or a ZIP for
Claude Cowork). The skill teaches the agent scene authoring, motion design,
keying/matting to transparent `.jet` overlays, tracking, beat sync and the full
render workflow. A copy lives in [`skills/strata-cli/`](skills/strata-cli/).
