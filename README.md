# magpie-plugins

Two [magpie](https://usemagpie.ai) plugins in one place: one watches the
gateway and mails you when something needs attention, the other gives
every agent a shared, deduplicated workspace instead of a fresh copy of
the tree. Each package is self-contained — the sections below are the
short version; the full docs live in each package's own README.

## mail-watch — email alerts from the gateway

`packages/mail-watch` · upstream PR:
[magpie-community/plugins#457](https://github.com/magpie-community/plugins/pull/457)

Watches, from beside the gateway, the two signals magpie already
records — and mails them through your own SMTP account, in your own
words. It has no page and no tab.

- **Provider errors** — a model call that ended in a 4xx/5xx, with the
  status, the vendor's error type and words, provider, model and agent.
  A try the gateway handed on to another provider is skipped: the
  request was answered on a later try, so it was no failure.
- **Quota windows** — a subscription account's allowance windows
  (`quotas.json`), each a percentage used: exhausted at 100%, or over
  the low line you set.
- **Cooldowns, not floods** — one mail per key per window
  (`cooldownMinutes`, plus a `maxMailsPerHour` budget for the whole
  hour), so a vendor's bad hour is one mail, not four hundred.
- **Opt-in usage digest** — set `report.hours` and it mails a periodic
  summary (calls, credits, tokens, sessions, agents, providers,
  models). Unset means none: filling in SMTP alone never starts one.
- **Your mailbox, your rules** — presets for the mailhosts people
  actually use (`qq`, `163`, `126`, `gmail`, `zoho`, `yahoo`, all TLS
  on 465), or host/port/secure by hand; TLS from the first byte by
  default, no STARTTLS. Zero npm dependencies: the SMTP client is
  hand-rolled over Bun's socket API, and the hook never awaits a send,
  so a broken mail server cannot touch a request.

```sh
magpie plugin add @magpie-community/hooks-mail-watch
```

Full options, template variables and behaviour:
[`packages/mail-watch/README.md`](packages/mail-watch/README.md).

## layerfs — one shared history for every agent

`packages/layerfs` · upstream PR:
[magpie-community/plugins#441](https://github.com/magpie-community/plugins/pull/441)

[LayerFS](https://github.com/Ephemeral-AI-Lab/layerfs) workspaces for
every agent magpie connects: instead of each agent copying a project
tree to work on it, agents share one durable, deduplicated history and
work through **stable filesystem paths**. Forking a branch for a
parallel agent copies **no file bytes**; a checkpoint stores **only
what changed**; any agent can resolve, read and build on what the
others committed.

Served on magpie's MCP server as `layerfs_<tool>`:

| Tool | What it does |
|---|---|
| `provision` | Gives the agent a workspace for a project branch and answers the path to work in. The first provision imports the source directory as the project's first checkpoint. |
| `checkpoint` | Saves the workspace's state on its branch, storing only the blobs the store has not seen. |
| `fork` | Forks a branch for a parallel agent: a pointer at an existing checkpoint, ready immediately, 0 bytes copied. |
| `publish` | Publishes a branch's head as main's new Layer — guarded, so main can only move forward. |
| `exec` | Runs a shell command in a branch's workspace and checkpoints whatever it changed as one commit. Disabled by default; runs only when the user has opted in. |
| `history` | Lists a branch's checkpoints, newest first, with logical size vs bytes actually stored. |
| `resolve` | Resolves a project / branch / file to a real filesystem path, its checkpoint and content hash. |
| `status` | Backend in use, projects and branches held, and the storage accounting. |

Plus a **trajectory page**: the host serves the replay of the
worktree history from its own localhost-only service, and the
installed page just navigates its frame to it — the install's folder
is never written into.

**This package is sim-only**: a local content-addressed store under
`MAGPIE_LAYERFS_HOME` (default `~/.layerfs-tools`). The native backend
— the real LayerFS through its JavaScript SDK — lives in the
standalone repo
[Mola-maker/magpie-layerfs](https://github.com/Mola-maker/magpie-layerfs)
and returns here once the SDK is published to npm.

```sh
magpie plugin add @magpie-community/tools-layerfs
```

Full tools, backend, safety defaults and page scheme:
[`packages/layerfs/README.md`](packages/layerfs/README.md).

## Layout

```text
packages/
  mail-watch/   mail-watch plugin (gateway hook, tests, icon)
  layerfs/      layerfs plugin (tools, trajectory page, tests, icon)
LICENSE         MIT
```

## Status

Both plugins are developed in
[magpie-community/plugins](https://github.com/magpie-community/plugins)
— the PRs linked above are where review happens. This repository is
their standalone home: the same packages, kept together.

## License

MIT — see [`LICENSE`](LICENSE).
