# Contributing to task

Thank you for your interest in contributing! Contributions from the community are welcome.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Ways to Contribute](#ways-to-contribute)
- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Pull Request Process](#pull-request-process)
- [Conventions](#conventions)
- [Communication](#communication)

## Code of Conduct

This project adheres to a [Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code.

## Ways to Contribute

- **Report bugs** - File detailed reports with reproduction steps
- **Suggest task files** - Propose new reusable Taskfiles or improvements
- **Write code** - Submit fixes or new task files
- **Improve documentation** - Fix typos, clarify content, add examples
- **Review pull requests** - Provide feedback on proposed changes

## Getting Started

1. **Fork the repository** on GitHub
2. **Clone your fork** locally:
   ```bash
   git clone https://github.com/YOUR_USERNAME/task.git
   cd task
   ```
3. **Add the upstream remote**:
   ```bash
   git remote add upstream https://github.com/vtmocanu/task
   ```
4. **Install the tooling** used by CI:
   ```bash
   # Task itself: https://taskfile.dev/installation
   pipx install yamllint
   brew install gitleaks   # or see https://github.com/gitleaks/gitleaks
   ```

## Development Workflow

1. **Create a branch** for your changes:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes** - add or edit a task file, update the README table

3. **Validate locally** - the same checks CI runs:
   ```bash
   yamllint .
   task -t <file>.yml --list-all   # confirm the file parses
   gitleaks git --staged --no-banner --redact
   ```

4. **Commit your changes**:
   ```bash
   git commit -m "Description of your changes"
   ```

5. **Push to your fork** and **open a Pull Request** on GitHub.

## Pull Request Process

### Before Submitting

- Ensure `yamllint .` and `task -t <file> --list-all` pass
- Keep the change focused on a single concern
- Update the README "Available task files" table if you add or rename a file

### PR Description

Include:

- **What** - Summary of changes
- **Why** - Motivation and context
- **Testing** - How you validated the change
- **Related Issues** - Link any related issues (e.g., "Fixes #123")

### Review Process

1. Automated checks (yamllint, task validation, gitleaks) run on your PR
2. A maintainer reviews your changes
3. Address any feedback
4. Once approved, a maintainer merges your PR

## Conventions

- **Overridable defaults**: every variable must have a `| default "..."` so a
  file works with zero configuration and is fully overridable per consumer.
- **Self-contained**: a task file includes nothing and references no private
  host, path, or credential. Use generic example hosts only.
- **Naming**: variables `UPPER_SNAKE_CASE`, task names `kebab-case`, internal
  helpers prefixed `_` with `internal: true`.
- Every public task has a `desc`.

## Communication

- **Issues** - Report bugs and request features via [GitHub Issues](https://github.com/vtmocanu/task/issues)

---

Thank you for contributing!
