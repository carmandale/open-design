# Napkin

## Corrections

- No corrections recorded yet.

## User Preferences

- Follow the repository AGENTS.md workflow and use `pnpm tools-dev` for local lifecycle work.
- Run Open Design on its **own pinned Node 24**, never the system node. The machine's system node is **Node 26**; OD pins **Node 24** (`engines.node: ~24`).

## Patterns That Work

- Read the repo napkin first; create it with the standard sections when missing.
- **Launch OD through its isolated toolchain, not the ambient node.** The repo already ships the safe environment — `mise.toml` (`node = "24"`, `pnpm = "10.33.2"`) + `.node-version`. One-time: `mise trust && mise install`. Then run via `mise exec -- pnpm tools-dev start web --daemon-port 17456 --web-port 17573`. mise's node lives at `~/.local/share/mise/installs/node/24/…`, fully separate from the system Node 26. `mise activate` is enabled in `~/.zshrc.local`, so `cd`-ing into the repo auto-selects Node 24 and a plain `pnpm tools-dev` then works. Alternatives that also pin 24: `nix develop` (flake `nodejs_24`) and Docker (`deploy/`, `node:24-alpine`).
- **After a large upstream merge, run `mise exec -- pnpm install` before starting.** `node_modules` goes stale (the 881-commit merge added `packages/metatool`, which needs `zod` — missing until install). Native `better-sqlite3` must match the running Node's ABI; staying on mise's Node 24 keeps it consistent (a version mismatch surfaces as a load/ABI error at daemon startup).

## Patterns That Don't Work

- Do not use root lifecycle aliases such as `pnpm dev` or `pnpm start` in this repository.
- Do not run `pnpm tools-dev` against the system node — it's Node 26 and OD requires ~24 (engines pin + `better-sqlite3` native ABI). Go through mise / Nix / Docker.

## Domain Notes

- Open Design local development is managed through the `tools-dev` control plane.
- Pinned toolchain: `mise.toml` (node 24, pnpm 10.33.2) + `.node-version` (`24`). Health check: `curl -s http://127.0.0.1:17456/api/health`.
- Web composer submits on **⌘/Ctrl+Enter** (the Send button's label flips to "Send" after the first turn; a bare click can no-op in automation — prefer the keyboard submit).
- The local runtime auto-detected here was **Codex CLI** (~8 min per generation — slow; Claude Code is typically faster). Generated artifacts save to `.od/projects/<id>/` as real portable HTML (e.g. `index.html` + a named file).
