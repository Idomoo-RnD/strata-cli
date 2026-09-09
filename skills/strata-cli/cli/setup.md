# Setup and libraries

What to check before installing anything, and the rule about which library a render lands in.

Part of the CLI reference — the index, and every other part, is in [commands.md](../commands.md).

## Setup — I check BEFORE installing anything
The `strata` CLI is a **standalone self-contained binary** (embeds its own runtime — I never install Node/npm for it).
1. **Check first:** `strata version`. If it prints, skip setup. Also try `~/.local/bin/strata` (Unix) and `%LOCALAPPDATA%\Programs\strata\strata.exe` (Windows).
2. **Only if missing:** Linux/macOS `curl -fsSL https://raw.githubusercontent.com/Idomoo-RnD/strata-cli/main/install.sh | bash` (set `STRATA_SKILL=skip STRATA_AUTH=skip` in agents — the installer otherwise prompts for skills and Idomoo credentials); Windows `irm https://raw.githubusercontent.com/Idomoo-RnD/strata-cli/main/install.ps1 | iex` (same env vars apply); or grab a binary from the [releases page](https://github.com/Idomoo-RnD/strata-cli/releases) and run by path.

## Libraries — ask, never pick

- **Library — I ASK before the first render, I never pick one myself.** The library is the user's Idomoo workspace: renders land in it and stay there, so choosing one silently puts their work somewhere they did not agree to. **If the user has not already named a library** (this conversation, a persisted `.idm-library` file, or the `library` preference in `strata prefs`), then before rendering I run **`strata library list`** and ask them, offering both:
     - **reuse an existing one** — I show the `id  name` rows so they can point at one (a series belongs in ONE library);
     - **create a new one** — I propose a name from the project and run `strata library create "<name>"` only after they say yes. `create` is idempotent: it reuses an id/name match instead of duplicating, and prints `♻️ reusing existing` when it does.

     If they answer with a name I have not seen, I do **not** assume it exists — `library create "<that name>"` resolves it either way. Then I **save the printed id** (persist it, e.g. a `.idm-library` file) and every later render passes that same `--library <id>`; it logs `Reusing library <id>` (if it logs `Created NEW library` I passed the wrong value). I switch only when the user says so. ⚠ `library list` is **paginated and omits a just-created library**, so an id missing from the listing is not proof it does not exist — pass the id and let `render` verify it.

     I never guess and never default — but a choice the user already made counts. `render` and `snapshot` resolve the library in this order: `--library` · a `.idm-library` file beside the scene or in the working directory · the `library` preference (`strata prefs set library <id>`), and log `library <id> from <source>`. With none of the three a non-interactive run **fails** with the library list rather than uploading somewhere arbitrary.
