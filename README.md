# codex

A Pointman extension that puts Codex CLI, OpenAI's coding agent, on your machines, so agents spawned as
`codex` run on it. It's a client: nothing runs beside it.
Its manifest says how a node installs it, signs it in and talks to it (`[client]`, v2).

```
pointman add codex
```

## How it works

- **Installing:** `pointman add codex` installs OpenAI's newest Codex CLI release binary from GitHub on every node
  of the workspace that hasn't got it, and on a node that joins later. `--nodes <machine>` adds it there only.
  It's checked against the sha-256 GitHub gives it and goes in the node's own folder, so it needs no root and
  never clashes with a Homebrew install. Nothing else is needed on the machine.
- **Yours stays yours:** a Codex CLI you installed yourself (Homebrew, npm, nix) counts as installed and comes
  first. `pointman add` leaves it as it is, `pointman upgrade` doesn't touch it, and `pointman remove` never
  takes it away.
- **Signing in:** sign Codex in once on each machine (`codex login`). `pointman health` and the apps'
  Machines page say when one is signed out.
- **Spawning:** a node runs agents on any client it has, so with Codex there, `/spawn <name> codex [machine]
  [~/folder]` in a thread starts an agent on it. Spawning on a machine without it is refused at once, with the
  fix: `pointman add codex --nodes <machine>`.

## Upgrade, remove, versions

- `pointman upgrade` moves the copies `pointman add` installed to their newest.
- `pointman remove codex` takes the node's own copy away, and nothing else.
- `pointman list` shows Codex on each machine, `added` (by `pointman add`) or `yours`, and `pointman health`
  its version, the newest there is, and whether it's signed in.
- It takes no version: a node always installs the newest. This extension's own version only marks changes
  to its manifest.

## Commands and settings

None. Codex keeps its own settings (`~/.codex/config.toml`), as it does outside Pointman.

## The manifest

```toml
[client]
npm = "@openai/codex"                       # where its newest version is said
binaries = ["codex"]
protocol = "codex-exec"                     # exec --json
home_env = "CODEX_HOME"                     # each agent's own home
api_keys = ["OPENAI_API_KEY", "CODEX_API_KEY"]   # never passed to a run: runs are on its own login

[client.install]
from = "release"                            # checked against GitHub's sha-256
repo = "openai/codex"
asset = "codex-{triple}.tar.gz"

[client.sign_in]
how = "codex login"
check = ["login", "status"]

[client.mcp]
via = "config-file"                         # its servers in config.toml, in each agent's home
file = "config.toml"
key = "mcp_servers"
rename = { headers = "http_headers" }
drop = ["type"]
```

`[client]` v2 (Pointman release 20) says what a node needs: its programs, how its runs talk (`protocol`), how a
node installs it, how it signs in, how MCP servers reach it, and its vendor's API keys, which never reach a run.
Codex CLI is built into the node as code, and this manifest has to say what that code does: a test in Pointman
checks they agree. A client still stands alone: no `[run]`, behaviour, files, links, rules or settings. The
format is in Pointman's docs/dev/extensions.md.

## Releasing

Bump `version` in extension.toml, commit, and make a GitHub release (not a draft or prerelease) tagged
`v<version>`. There's nothing to build, so it has no assets. The marketplace lists it within 30 minutes.

## Licence

MIT. See `LICENSE`.
