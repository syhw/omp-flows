# omp-flows

Multi-agent flows for [OMP](https://omp.sh), written as plain subagent definitions. The repository is an OMP marketplace; each flow is a plugin you install, toggle, and upgrade on its own.

| plugin       | entry agent  | what it runs                                                                                                                                         | source                                                                                         |
| ------------ | ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `team-solve` | `team-solve` | plan → ideate A ∥ ideate B → solve → test → review → repair → verify, every role a fresh agent talking only through files                             | [memdoor team-solve](https://github.com/guregodevo/memdoor/tree/main/examples/workflows/team-solve) |
| `srchd`      | `srchd`      | rounds of `srchd-researcher` agents that publish, peer-review, cite, and report solutions through a file-based publication system                    | [dust-tt/srchd](https://github.com/dust-tt/srchd)                                             |

## Install on a new machine

```sh
omp plugin marketplace add git@github.com:syhw/omp-flows.git
omp plugin install team-solve@omp-flows
omp plugin install srchd@omp-flows
```

In a running session, run `/reload-plugins` afterwards. `/plugins disable <name>` turns a flow off without uninstalling it.

## Update

```sh
omp plugin marketplace update omp-flows
omp plugin upgrade
```

`upgrade` reinstalls only plugins whose `version` in `.omp-plugin/marketplace.json` changed. To check at startup instead, set `marketplace.autoUpdate: auto` in `~/.omp/agent/config.yml`.

## Use

- **team-solve**: "Use the team-solve agent on `<dir>`". `<dir>` holds `task.md` and `tests/N.in` / `tests/N.out`; the program is `solution.py`, run with Python 3. The verdict is PASS only if every sample passes.
- **srchd**: "Use the srchd agent on `<dir>` with 5 researchers, 4 reviewers, 3 rounds" (the defaults). `<dir>` holds `problem.md`; state lives in `<dir>/srchd/`. Running it again on the same directory continues the experiment. Researchers run `bash` on the host, not in a container.

Round length for srchd is set by how long researchers work; cap it per session with `task.agentSoftRequestBudget: { srchd-researcher: <requests> }`. No agent pins a model; route one with `task.agentModelOverrides`.

## Develop

Link the checkout instead of installing from the marketplace, so edits apply on the next spawn:

```sh
omp plugin link ./team-solve
omp plugin link ./srchd
```

A machine uses either the links or the marketplace install for a plugin, not both: they register the same package name.

## Release

1. Edit the agent files under `<plugin>/agents/`.
2. Bump the plugin's version in both `<plugin>/package.json` and `.omp-plugin/marketplace.json`.
3. Commit, tag `<plugin>-v<version>`, push.

## Layout

```text
.omp-plugin/marketplace.json   catalog: plugin names, sources, versions
team-solve/                    package.json, LICENSE (Apache-2.0, memdoor), agents/*.md
srchd/                         package.json, LICENSE (MIT, srchd), agents/*.md
```
