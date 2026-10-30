# google-maps-scraper — Security Principles

## 1. Pin GitHub Actions to commit SHAs, not version tags

- `uses: actions/checkout@v4` trusts whoever controls that tag right now —
  if the action's maintainer account is compromised, or the tag is
  repointed, your CI runs arbitrary code with access to whatever secrets
  that workflow has (here: `DOCKER_USERNAME`/`DOCKER_PASSWORD` in
  `publish.yml`, used to push to Docker Hub).
- Always pin to the resolved commit SHA, with the version kept as a trailing
  comment for readability:
  `uses: actions/checkout@<sha> # v4`
- Resolve the SHA with `git ls-remote https://github.com/<org>/<repo>.git refs/tags/<tag> refs/tags/<tag>^{}`
  (or `gh api repos/<org>/<repo>/git/refs/tags/<tag>`) and verify it
  independently before trusting it.
- When bumping an action's version, re-resolve and re-pin the SHA — don't
  just bump the comment.

## 2. nginx/CI config gets the same scrutiny as application code

- A missing semicolon or an overly permissive directive in `.github/workflows/*.yml`
  is a real bug, not just formatting — it's reviewed like any other change.
