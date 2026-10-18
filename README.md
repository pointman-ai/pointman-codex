# codex

A Pointman extension that puts Codex CLI, OpenAI's coding agent, on your machines, so agents spawned as
`codex` run on it. It's a client: nothing runs beside it. The extension is only the name of npm's package,
`@openai/codex`, and each node installs it.

```
pointman add codex
```

## How it works

- **Installing:** `pointman add codex` installs npm's newest Codex CLI on every node of the workspace that
  hasn't got it, and on a node that joins later. `--nodes <machine>` adds it there only. It goes in the node's
  own folder, never npm's global one, so it needs no root and never clashes with a Homebrew install. The
  machine needs npm (Node.js).
- **Yours stays yours:** a Codex CLI you installed yourself (Homebrew, npm, nix) counts as installed and comes
  first. `pointman add` leaves it as it is, `pointman upgrade` doesn't touch it, and `pointman remove` never
  takes it away.
- **Signing in:** sign Codex in once on each machine (`codex login`). `pointman health` and the apps'
  Machines page say when one is signed out.
- **Spawning:** a node runs agents on any client it has, so with Codex there, `/spawn <name> codex [machine]
  [~/folder]` in a thread starts an agent on it. Spawning on a machine without it is refused at once, with the
  fix: `pointman add codex --nodes <machine>`.

## Upgrade, remove, versions

- `pointman upgrade` moves the copies `pointman add` installed to npm's newest.
- `pointman remove codex` takes the node's own copy away, and nothing else.
- `pointman list` shows Codex on each machine, `added` (by `pointman add`) or `yours`, and `pointman health`
  its version, the newest there is, and whether it's signed in.
- It takes no version: a node always installs npm's newest. This extension's own version only marks changes
  to its manifest.

## Commands and settings

None. Codex keeps its own settings (`~/.codex/config.toml`), as it does outside Pointman.

## The manifest

```toml
[client]
npm = "@openai/codex"
```

That's all a client is: npm's package. A client has no `[run]`, behaviour, files, links, rules or settings.
Each node installs from its own table of packages, which has to agree with this one.

## Releasing

Bump `version` in extension.toml, commit, and make a GitHub release (not a draft or prerelease) tagged
`v<version>`. There's nothing to build, so it has no assets. The marketplace lists it within 30 minutes.

## Licence

MIT. See `LICENSE`.
