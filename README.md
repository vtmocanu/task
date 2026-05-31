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
`TASK_X_REMOTE_TASKFILES=1`. Local includes (the `else` branch) need no flag.

Run `task -t hugo.yml --list` to see every task and its description.

## Conventions

- Every variable has a sensible default, so a file works with zero config and
  stays fully overridable per consumer.
- Files are self-contained: they include nothing and reference no private host,
  path, or credential. Generic example hosts only.

## Contributing

Issues and PRs welcome. See [CONTRIBUTING](CONTRIBUTING.md),
[CODE_OF_CONDUCT](CODE_OF_CONDUCT.md), and [SECURITY](SECURITY.md).

## License

[MIT](LICENSE) (c) Vlad Mocanu
