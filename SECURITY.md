# Security Policy

## Reporting Security Issues

**Please do not report security issues through public GitHub issues.**

Use GitHub's [private vulnerability reporting](https://github.com/vtmocanu/task/security/advisories/new) for this repository, or email [vtmocanu@gmail.com](mailto:vtmocanu@gmail.com).

Please include:

- Description of the issue
- Steps to reproduce
- Affected files or versions
- Potential impact
- Any suggested fix (if available)

## Response Timeline

I will acknowledge your report within **3 business days** and provide a more detailed response within **7 business days** indicating the next steps.

## Scope

This repository contains reusable Taskfiles only. They reference no private host, path, or credential, and run whatever the consumer's variables point them at. The most relevant concerns are:

- A task file shipping a secret or an internal hostname (it should never).
- A task definition that runs an unexpected or unsafe command.

Both are in scope. The local pre-commit hook and CI both run `gitleaks`, and CI also lints and validates every task file, as a backstop.

## Supported Versions

| Version | Supported |
| ------- | --------- |
| 0.x     | Yes       |

## Best Practices

When including a task file from this repository:

- Pin to a tag or commit rather than a moving branch for reproducible builds.
- Review the task file before including it; you are running its commands.
- Override the provided defaults to match your environment.

Thank you for helping keep this project and its users safe.
