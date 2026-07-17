# GitHub Copilot instructions

## Repository contract

- Read the repository-root `README.md` and `SECURITY.md` before changing anything. Read repository-root `AGENTS.md` and `CLAUDE.md` if they are added; they are not currently present, so do not depend on hidden or machine-local copies.
- This public profile repository describes selected public work around private local systems. Stop when required local-only context is unavailable; do not invent it. Never disclose private implementation details, personal context, client data, local paths, or absent source material.
- Keep public claims grounded in repository evidence and preserve the disclosure guidance in `SECURITY.md`.

## Workflow and Git

- Work in an isolated task checkout: use a new worktree for local Copilot work or the provider's isolated sandbox for cloud work. Use a conventional, task-based branch and commit name; never use an agent name as the branch prefix.
- Keep the diff narrow, verify every affected link and asset reference, and stage only intended paths.
- Never push directly to `main`, force-push `main`, merge a pull request, bypass hooks, or publish private operational detail. Open a pull request and wait for the required `ci` check before merge.
- Resolve every review thread or explain the evidence for rejecting it. Copilot review is advisory and does not replace CI or owner approval.

## Dependencies and security

- This repository has no application dependency manifest. Do not add a top-level dependency, package manifest, lockfile, build system, or lifecycle script without explicit approval from the owner and a repository-local security rationale in the pull request.
- Never commit credentials, `.env` files, personal data, private infrastructure details, or generated artifacts.

## Repository commands

This is a static profile repository with no local build, lint, or unit-test command. Perform repository-native checks only:

```bash
git diff --check
git diff --name-only --diff-filter=ACDMRTUX
```

Review changed Markdown links and asset paths against files in the checkout. GitHub Actions runs the secret scan in `.github/workflows/ci.yml`; its aggregate required check is `ci`. Do not claim the secret scan passed until that check is green.

## Review priorities

Prioritize silent failures, boundary validation, tests when present (otherwise CI checks), security, unresolved review threads. Also review accidental private disclosure, unsupported public claims, broken links/assets, and rendering regressions.
