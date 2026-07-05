# Profile Repository Security Hardening Evidence - 2026-07-05

## Target

- Repository: `sterngold/sterngold`
- Base commit: `473520a6df3a546fad7dee55488b7b2e52bcb6d8`
- Purpose: public GitHub profile README repository
- Risk class: R2 public identity anchor

## Finding Restatement

The Codex Security review did not identify a classic application vulnerability.
The repository has no package manifest, runtime code, database, server route, or
client-side JavaScript. The concrete security boundary is public profile trust:
README links, Git history disclosure, vulnerability-reporting path, and future
GitHub Actions execution.

## Findings Addressed

- Current profile links use HTTPS and avoid unsafe Markdown protocols.
- Current tip and bounded README/security/workflow history checks found no
  obvious active credential patterns.
- Local `gitleaks detect --source . --redact --verbose` scanned 14 commits and
  reported no leaks before this change.
- GitHub Actions used tag-pinned third-party actions; these are now pinned by
  immutable commit SHA.
- Security reporting now points directly to GitHub private vulnerability
  reporting for this repository.

## Verification Commands

```bash
find . -maxdepth 3 -type f ! -path './.git/*' | sort
find . -maxdepth 3 \( -name 'package.json' -o -name 'package-lock.json' -o -name 'pyproject.toml' -o -name 'requirements*.txt' -o -name 'Dockerfile' \) -print
git grep -nE 'http://|javascript:|data:|file:|count_private|github-readme-stats|eventradar-site|@[A-Za-z0-9._%+-]+\.[A-Za-z]{2,}' -- README.md SECURITY.md .github || true
git log --all --format='%H' | while read c; do
  git grep -I -nE 'ghp_[A-Za-z0-9_]{36}|github_pat_[A-Za-z0-9_]{20,}|sk-[A-Za-z0-9]{20,}|AKIA[0-9A-Z]{16}|-----BEGIN (RSA |OPENSSH |EC |DSA )?PRIVATE KEY-----|password\s*[:=]|api[_-]?key\s*[:=]' "$c" -- README.md SECURITY.md .github 2>/dev/null
done
gitleaks detect --source . --redact --verbose
python3 - <<'PY'
import pathlib, re
p = pathlib.Path(".github/workflows/ci.yml")
text = p.read_text()
assert re.search(r"(?m)^permissions:\n  contents: read$", text)
uses = re.findall(r"uses:\s*([^ \n]+)", text)
assert "actions/checkout@9c091bb21b7c1c1d1991bb908d89e4e9dddfe3e0" in uses
assert "gitleaks/gitleaks-action@e0c47f4f8be36e29cdc102c57e68cb5cbf0e8d1e" in uses
assert all(len(u.rsplit("@", 1)[1]) == 40 for u in uses)
print("workflow pins and read-only permissions verified")
PY
```

## Local Verification Results

- File-surface check listed only `README.md`, `SECURITY.md`,
  `.github/dependabot.yml`, `.github/workflows/ci.yml`, and three image assets
  under `assets/`.
- Package/runtime manifest search returned no files.
- Current-tip unsafe protocol/disclosure grep returned no matches for
  `http://`, `javascript:`, `data:`, `file:`, direct email pattern,
  `count_private`, `github-readme-stats`, or `eventradar-site`.
- Bounded history secret-pattern grep over `README.md`, `SECURITY.md`, and
  `.github` returned no matches.
- `gitleaks detect --source . --redact --verbose` scanned 14 commits and
  reported no leaks.
- Workflow assertion printed:
  `workflow pins and read-only permissions verified`.

## GitHub Settings Snapshot Before External Writes

- Private vulnerability reporting: enabled.
- Workflow default permissions: `contents: read`.
- Actions repository policy: `allowed_actions=all`, `sha_pinning_required=false`.
- Branch protection on `main`: strict `ci` required, required linear history
  enabled, force pushes disabled, deletions disabled, conversation resolution
  required, admin enforcement disabled.
- Collaborators: only `sterngold`, admin.

## Residuals

- GitHub repository Actions policy still needs an external settings write to
  move from `allowed_actions=all` to selected actions.
- GitHub account-level controls must be checked in GitHub settings: passkeys or
  hardware-key MFA, active sessions, PAT scopes, OAuth apps, SSH keys, deploy
  keys, and domain registrar access.
- A Git history rewrite is not recommended unless a confirmed active secret is
  discovered. Historical contact/project wording is an OSINT residual, not a
  credential incident.
- If future workflows are added, keep `permissions: contents: read`, avoid
  `pull_request_target`, and pin every third-party action by SHA.
