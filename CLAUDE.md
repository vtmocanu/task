# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Public-safe library of reusable [Task](https://taskfile.dev) (`go-task`) files. Each `*.yml` at the repo root is a self-contained, parameterised Taskfile that a consumer repo includes and overrides with its own `vars`. Currently: `hugo.yml` (Hugo dev-server lifecycle, zero-config) and `homebrew-tap.yml` (render a Homebrew formula from a consumer-owned `Formula.rb.tmpl` and push it to a tap; consumed by `fj-queue`).

This is `github.com/vtmocanu/task` (GitHub, MIT-licensed, public). It is the public counterpart to the private internal `wxs/task` library on Forgejo. **Nothing private or sensitive belongs here**: no internal hosts, repo slugs, node names, IPs, tokens, or internal-only paths in code, comments, or examples. Use generic example hosts only. `gitleaks` runs in CI and as a pre-commit backstop.

Because it is a GitHub repo, use the **`gh`** CLI for repo operations — never `tea`/`fj-ex` (those are Forgejo-only).

## Validate locally (same checks as CI)

```bash
yamllint .                          # lint all YAML (config: .yamllint.yml)
task -t hugo.yml --list-all         # confirm a file parses; repeat per *.yml
gitleaks git --staged --no-banner --redact   # secret scan
```

There is no build or test suite — "tests" means: the file lints, parses, and its tasks behave. CI ([.github/workflows/ci.yml](.github/workflows/ci.yml)) runs three jobs: `yamllint`, `validate-tasks` (loops `*.yml` through `task -t … --list-all`), and `gitleaks`. GitHub Actions are SHA-pinned with a version comment and use `persist-credentials: false`.

## How consumers use a file

A consumer includes a file with a CI/local conditional so a local checkout is used in dev and the public raw URL in CI. Local includes need no flag; the remote (CI) branch needs `TASK_X_REMOTE_TASKFILES=1`:

```yaml
includes:
  hugo:
    taskfile: "{{if .CI}}https://raw.githubusercontent.com/vtmocanu/task/main/hugo.yml{{else}}~/stuff/gitrepos/gh/vtmocanu/task/hugo.yml{{end}}"
    vars:
      HUGO: "devbox run hugo"   # override any var
```

## Authoring conventions (enforced by review, partly by CI)

- **Every configuration knob has `| default "…"`** and stays overridable per consumer. See the `vars:` block in `hugo.yml` for the pattern. Vars that identify the consuming project (no universal default, e.g. `NAME`/`GH_REPO`/`TAP_REPO` in `homebrew-tap.yml`) are declared via `requires` instead. Resolve consumer file paths against `{{.ROOT_DIR}}` (the consumer's root Taskfile dir), never `{{.TASKFILE_DIR}}` (which is this file's location — the remote cache when included over https).
- **Self-contained**: a file `includes` nothing and references no private host, path, or credential.
- **Naming**: vars `UPPER_SNAKE_CASE`, task names `kebab-case`, internal helpers prefixed `_` with `internal: true`. Every public task has a `desc`.
- **`---` document-start marker** is required (yamllint `document-start: present: true`).
- Use the **`.yml`** extension, not `.yaml`.
- When adding or renaming a file, **update the "Available task files" table in [README.md](README.md)**.

## Detaching background processes (load-bearing gotcha)

Task's embedded shell (`mvdan/sh`) reaps plain `&` background jobs when the script exits and has no `disown` builtin. To start a long-lived process that survives the task, wrap it in real bash:

```yaml
bash -c "nohup mycmd > '{{.LOG}}' 2>&1 & disown"
```

This is why `hugo:start` (`hugo.yml:42-46`) shells out to `bash -c '… & disown'` rather than backgrounding directly. Use a `status:` precondition (e.g. a `curl` health check) to make such start tasks idempotent.
