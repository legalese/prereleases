# prereleases — the L4 binary shelf

Standalone `l4` and `jl4-lsp` binaries, built from
[`legalese/l4-ide`](https://github.com/legalese/l4-ide)'s long-lived
integration branch `unstable` and published to **this repo's**
[Releases page](https://github.com/legalese/prereleases/releases).

**This is not the stable line and carries no compatibility promise.** Stable
builds are the VS Code extensions released from `l4-ide`'s `main` under
`l4-ide-build-<n>` tags.

## Why a separate repo

Releases and tags are repo-scoped, so prerelease archives cut from `unstable`
would otherwise land on `l4-ide`'s releases page beside the stable extension
builds — a surface its maintainers keep for the stable track (the one
prerelease published there, `unstable-20260805-c873bb5`, was deleted within
days). This repo is the shelf: its releases page and tag namespace exist for
nothing else, and `l4-ide` carries no trace — no workflow runs, no tags, no
releases.

## Getting a build

Tags are `unstable-<YYYYMMDD>-<short-sha>`; the SHA names the `l4-ide` commit
the archive was built from. Each release carries one archive per platform
(`linux-x64`, `darwin-arm64`, `win32-x64`) plus `SHA256SUMS`; each archive
extracts to a directory of its own name holding `l4`, `jl4-lsp`, a
`libraries/` copy of the L4 standard library, and `BUILD-INFO.txt`. The
release body carries copy-paste download instructions.

## Cutting a build

`.github/workflows/prerelease.yml`, dispatched by hand (Actions → Run
workflow, or `gh workflow run prerelease.yml --repo legalese/prereleases`).
`dry_run` defaults to **true** — a full rehearsal that builds, smoke-tests
and checksums everything but creates no tag and no release. Uncheck it to
publish. Publishing is an outward-facing act; a human decides when.

## Using a shelf build in VS Code's L4 extension

The extension and this shelf serve different goals — the extension bundles
whatever `l4`/`jl4-lsp` shipped with its own stable release, and a shelf
build lets you run the editor against a specific `unstable` commit instead
(a fix that hasn't reached a stable extension release yet, or a bisection).
Two binaries, two separate steps — the extension has no single "use this
build" switch.

**`jl4-lsp` (language features — diagnostics, hover, go to definition, etc.):**

1. Download and extract the archive for your platform (see "Getting a
   build" above), e.g. to `~/l4-builds/l4-unstable-20261001-abc1234-darwin-arm64/`.
2. macOS only: clear the quarantine attribute before VS Code (or anything
   else) will run it —
   `xattr -dr com.apple.quarantine ~/l4-builds/l4-unstable-20261001-abc1234-darwin-arm64`.
3. In VS Code, open Settings (`Cmd+,` / `Ctrl+,`), search for
   `jl4.serverExecutablePath`, and set it to the full path of the extracted
   `jl4-lsp` binary — e.g.
   `/Users/you/l4-builds/l4-unstable-20261001-abc1234-darwin-arm64/jl4-lsp`
   (`jl4-lsp.exe` on Windows). Equivalently, in `settings.json`:

   ```json
   "jl4.serverExecutablePath": "/Users/you/l4-builds/l4-unstable-20261001-abc1234-darwin-arm64/jl4-lsp"
   ```

4. Run **Developer: Reload Window** (or restart VS Code). The extension's
   Output panel (the "L4" channel) logs
   `[client] Using configured server path: ...` on connect, confirming it
   picked up the shelf build rather than its own bundled binary or whatever
   `jl4-lsp` is on `PATH` — that's the extension's full resolution order, in
   that priority.
5. To go back to the extension's own bundled binary, clear the setting
   (empty string) and reload again.

**`l4` (the standalone CLI — `l4 check`, `l4 export`, `l4 batch`, etc.):**

The extension's **L4: Install L4 CLI** command (`l4.installCli`) only installs
the binary bundled *inside the extension*; there's no setting that points it
at an external build. To use the shelf's `l4` instead, put it on your `PATH`
yourself, ahead of (or in place of) anything `l4.installCli` already
installed — e.g. on macOS/Linux:

```bash
ln -sf ~/l4-builds/l4-unstable-20261001-abc1234-darwin-arm64/l4 ~/.local/bin/l4
```

(That's the same location `l4.installCli` uses, so this simply overwrites
its symlink.) Confirm with `l4 --help` in a fresh terminal — `BUILD-INFO.txt`
inside the extracted archive records the exact commit it was built from.
