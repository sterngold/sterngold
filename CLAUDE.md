# sterngold/sterngold

Vlad's public GitHub profile README repo — `README.md` on `main` renders on github.com/sterngold. See @README.md for current content and @.github/copilot-instructions.md for the full repository contract (read that file first; its command block is authoritative).

## Commands

Static profile repository, no local build, lint, or unit-test command. Repository-native checks only:

```bash
git diff --check
git diff --name-only --diff-filter=ACDMRTUX
```

Review changed Markdown links and asset paths against files in the checkout.

## What a merge does

The profile is live the moment `main` changes — GitHub renders `main`'s `README.md` directly on github.com/sterngold, no build or deploy step. `.github/workflows/ci.yml` runs a `gitleaks` secret scan on every push to `main` and on PRs, aggregated into a required `ci` check that branch protection enforces before merge (linear history required, force-push and branch deletion blocked).

## Hard rules

- Never push directly to `main`, force-push, merge without owner approval, bypass hooks, or publish private operational detail — because this repo is a public identity anchor; the cost of a mistake here is public, not local.
- No package manifest, lockfile, build system, or lifecycle script without explicit owner approval and a security rationale in the PR — because the repo currently has zero dependency surface by design.
- Never commit credentials, `.env` files, personal data, private infrastructure details, or generated artifacts — because this repo describes private systems without exposing them.
- Stop when required local-only context is unavailable; never invent it — because public claims must stay grounded in repository evidence.
- Work in an isolated checkout (new worktree, or the provider's sandbox for cloud work) on a conventional, task-based branch name — never an agent-name prefix.
