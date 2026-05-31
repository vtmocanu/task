# task

Reusable [Task](https://taskfile.dev) (`go-task`) files I share across my public
projects. Each file is a self-contained, parameterised Taskfile that a consumer
repo includes and overrides with its own variables.

[![ci](https://github.com/vtmocanu/task/actions/workflows/ci.yml/badge.svg)](https://github.com/vtmocanu/task/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)

## Available task files

| File | Tasks | Purpose |
|------|-------|---------|
| [`hugo.yml`](hugo.yml) | `start` `stop` `restart` `build` `status` | Hugo static-site dev server lifecycle |
| [`homebrew-tap.yml`](homebrew-tap.yml) | `formula` `publish` | Render a Homebrew formula from a template and push it to a tap |

## Usage

Include a file in your own `Taskfile.yml`. The conditional below uses a local
checkout while developing and the public raw URL in CI:

```yaml
version: "3"

vars:
  TASK_REPO_LOCAL: ~/stuff/gitrepos/gh/vtmocanu/task
  TASK_REPO_RAW: https://raw.githubusercontent.com/vtmocanu/task/main

includes:
  hugo:
    taskfile: "{{if .CI}}{{.TASK_REPO_RAW}}/hugo.yml{{else}}{{.TASK_REPO_LOCAL}}/hugo.yml{{end}}"
    vars:
      HUGO: "hugo"          # or "devbox run hugo", "mise exec -- hugo", etc.

tasks:
  default:
    cmds:
      - task --list
```

Then:

```bash
task hugo:start     # detached dev server
task hugo:status
task hugo:restart
task hugo:stop
task hugo:build     # production build
```

Remote includes are an experimental Task feature, enabled with
`TASK_X_REMOTE_TASKFILES=1` (and `task --yes` in CI to auto-confirm the one-time
remote-trust prompt). Local includes (the `else` branch) need no flag.

Run `task -t hugo.yml --list` to see every task and its description.

### homebrew-tap.yml

This file renders a Homebrew formula and publishes it to a tap. Unlike
`hugo.yml`, it needs a few identity vars (declared via `requires`, no universal
default): `NAME`, `GH_REPO`, `TAP_REPO`, plus `VERSION` and `HOMEBREW_TAP_TOKEN`
per invocation. The formula body lives in the consumer repo as a template
(default `Formula.rb.tmpl`) with `@@URL@@` / `@@SHA256@@` / `@@VERSION@@`
placeholders, so this file stays project-agnostic.

```yaml
includes:
  brew:
    taskfile: "{{if .CI}}{{.TASK_REPO_RAW}}/homebrew-tap.yml{{else}}{{.TASK_REPO_LOCAL}}/homebrew-tap.yml{{end}}"
    vars:
      NAME: my-tool
      GH_REPO: youruser/my-tool
      TAP_REPO: youruser/homebrew-tap
```

```bash
task brew:formula VERSION=v1.2.3                            # render dist/my-tool.rb
task brew:publish VERSION=v1.2.3 HOMEBREW_TAP_TOKEN=<pat>   # push to the tap
```

## Conventions

- Every configuration knob has a sensible default and is overridable per
  consumer. Vars that identify the consuming project (and so have no universal
  default) are declared via `requires` instead.
- Files are self-contained: they include nothing and reference no private host,
  path, or credential. Generic example hosts only.

## Contributing

Issues and PRs welcome. See [CONTRIBUTING](CONTRIBUTING.md),
[CODE_OF_CONDUCT](CODE_OF_CONDUCT.md), and [SECURITY](SECURITY.md).

## License

[MIT](LICENSE) (c) Vlad Mocanu
