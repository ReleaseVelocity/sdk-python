# PR Replication Methodology

This document describes how eval sample PRs are replicated from `strands-agents/sdk-python` into `ReleaseVelocity/sdk-python` to produce reproducible CI failures for the release_pilot_triage benchmark.

## Goal

Replicate PRs that produce specific CI failures (lint, unit test, integration test) so an agent can be evaluated on its ability to triage those failures.

## Approach

Each replicated PR uses a **base branch** and a **fail branch**:

```
merge-base (from original repo main)
  └── [ci: setup workflows]  →  base-{PR_NUM}   (PR target)
        └── [cherry-picked PR commits...]  →  fail-{PR_NUM}   (PR source)
```

### Key Principles

1. **Base branch** = the merge-base between `origin/main` and the failing `head_sha`, plus a workflow setup commit
2. **Fail branch** = built on top of the base branch, with all original PR commits cherry-picked in order
3. **Workflow files are identical** on both branches — so the PR diff only shows source code changes
4. **Full commit history is preserved** — the PR shows the same commits as the original

## Steps to Replicate a PR

Given a dataset sample with:
- `head_sha`: the commit that failed CI
- `pull_url`: link to the original PR

### 1. Find the merge-base

```bash
cd /tmp/sdk-python
MERGE_BASE=$(git merge-base origin/main $HEAD_SHA)
```

If the commit is missing, try fetching the PR ref:
```bash
git fetch origin pull/{PR_NUM}/head
```

### 2. Create the base branch

```bash
git checkout $MERGE_BASE -B base-{PR_NUM}

# Replace all workflow files with the standardized set
rm -rf .github/workflows/
mkdir -p .github/workflows/
git checkout rv-main -- .github/workflows/
git add .github/workflows/
git commit -m "ci: setup workflows"

git push rv HEAD:base-{PR_NUM} --force
```

### 3. Create the fail branch

```bash
# Start from the base branch
git checkout -B fail-{PR_NUM}

# Cherry-pick all commits between merge-base and head_sha (oldest first)
COMMITS=$(git rev-list --reverse $MERGE_BASE..$HEAD_SHA)
for commit in $COMMITS; do
  git cherry-pick $commit --no-commit || true
  # Restore workflow files so they don't appear in the PR diff
  git checkout base-{PR_NUM} -- .github/workflows/
  git add -A
  git commit -m "$(git log --format=%s -1 $commit)" --allow-empty
done

git push rv HEAD:fail-{PR_NUM} --force
```

### 4. Create the PR

```bash
curl -X POST -H "Authorization: token $GH_TOKEN" \
  -d '{"title":"...","head":"fail-{PR_NUM}","base":"base-{PR_NUM}","body":"..."}' \
  "https://api.github.com/repos/ReleaseVelocity/sdk-python/pulls"
```

## Workflow Configuration

### `pr-and-push.yml` (Pull Request and Push Action)

Triggers on PRs targeting `main` or `base-*` branches. Runs:
- **call-test-lint / Lint** — ruff linting via `hatch fmt --linter --check`
- **call-test-lint / Unit Tests** — pytest across Python 3.10-3.14 on linux/windows/macOS
- **check-api** — griffe API breaking changes check against the PR base branch

### `integration-test.yml` (Secure Integration test)

Triggers on `pull_request_target` for `main` or `base-*` branches. Runs:
- **check-access-and-checkout** — AWS credential setup + integration tests (fails without secrets)

### `test-lint.yml`

Reusable workflow called by `pr-and-push.yml`. Contains the Lint and Unit Test job definitions.

## Current Replication Status

| Our PR | Original PR | Commits | Expected Failure Job |
|--------|-------------|---------|---------------------|
| #49 | PR#1498 | 3 | `call-test-lint / Lint` |
| #50 | PR#1734 | 1 | `call-test-lint / Lint` |
| #51 | PR#1948 | 4 | `call-test-lint / Unit Tests - Python 3.14 - windows` |
| #52 | PR#2116 | 2 | `call-test-lint / Unit Tests - Python 3.13 - windows` |
| #53 | PR#2245 | 2 | `call-test-lint / Lint` |
| #54 | PR#2153 | 1 | `check-access-and-checkout` |

### Cannot Replicate (commits force-pushed away)

| Original PR | Job | Missing head_sha |
|-------------|-----|-----------------|
| PR#2123 | `check-api` | `88e729263102...` |
| PR#1719 | `check-api` | `52f9f9dc5ebf...` |
| PR#1706 | `check-access-and-checkout` | `084247933b1f...` |
| PR#1562 | `check-access-and-checkout` | `975549cc0f63...` |
| PR#1567 | `check-api` | `d1d234f1ea1b...` |

## Troubleshooting

### CI not triggering
- The workflow `on.pull_request.branches` filter must include `'base-*'`
- GitHub uses the workflow file **from the base branch** to decide whether to trigger

### Workflow files appearing in PR diff
- Both `base-{PR_NUM}` and `fail-{PR_NUM}` must have identical `.github/workflows/` content
- The fail branch must be built **on top of** the base branch (not independently from the original commit)

### Missing commits
- Force-pushed branches lose old commits from GitHub
- Try `git fetch origin pull/{PR_NUM}/head` — if the commit existed at any point in the PR, it may still be reachable
- If not, the commit is unrecoverable and the sample cannot be replicated
