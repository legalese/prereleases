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
