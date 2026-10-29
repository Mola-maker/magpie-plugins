# magpie-plugins

Standalone home for two magpie plugins, developed in
[magpie-community/plugins](https://github.com/magpie-community/plugins):

- **mail-watch** (`packages/mail-watch`) — email alerts from the gateway:
  provider errors and exhausted quotas, with your own recipients,
  templates and watch list. Upstream PR:
  [magpie-community/plugins#457](https://github.com/magpie-community/plugins/pull/457).
- **layerfs** (`packages/layerfs`) — LayerFS workspaces for every agent
  in magpie: deduplicated checkpoints and branches behind stable
  filesystem paths, instead of a fresh copy of the tree for every
  agent, with a page that replays the worktree trajectory. Upstream PR:
  [magpie-community/plugins#441](https://github.com/magpie-community/plugins/pull/441).

Each package is self-contained — see its own README for details. Both
are MIT licensed (see `LICENSE`).
