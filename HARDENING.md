<!-- markdownlint-disable -->

# Hardening Report: peaceiris--actions-gh-pages/v4.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **peaceiris--actions-gh-pages/v4.0.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: GitHub Actions expressions are interpolated directly inside a `run:` shell command string. In pages-status-check.yml, `${{ github.event.build.status }}` (line 11) and `${{ github.event.build.error.message }}` (line 12) are embedded directly in a Python `run:` block. An attacker who can influence the page_build event payload could inject arbitrary Python code.

Locations:

- `.github/workflows/pages-status-check.yml:11`
- `.github/workflows/pages-status-check.yml:12`

### script-injection (severity: high)

Rule (a) violation: `${{ secrets.GITHUB_TOKEN }}` is interpolated directly inside a bash `run:` block in update-major-tag.yml (line 17): `git remote set-url origin "https://x-access-token:${{ secrets.GITHUB_TOKEN }}@github.com/${GITHUB_REPOSITORY}.git"`. Any `${{ ... }}` expression directly inside a `run:` script is a script-injection finding per the check rules.

Locations:

- `.github/workflows/update-major-tag.yml:17`

### unpinned-uses (severity: high)

All `uses:` references across workflow files use mutable tag/version strings instead of immutable 40-character SHA commit hashes, making the workflows vulnerable to supply-chain attacks if any referenced action is compromised or its tag is moved. Failing references include: actions/checkout@v4, peaceiris/workflows/setup-node@v0.19.1, actions/upload-artifact@v4, codecov/codecov-action@v3, peaceiris/actions-mdbook@v1.2.0, github/codeql-action/init@v3, github/codeql-action/autobuild@v3, github/codeql-action/analyze@v3, actions/dependency-review-action@v4, peaceiris/actions-label-commenter@v1, peaceiris/actions-github-app-token@v1.1.6, actions/labeler@v4, peaceiris/workflows/create-release-npm@v0.19.1.

Locations:

- `.github/workflows/test.yml:27`
- `.github/workflows/test.yml:29`
- `.github/workflows/test.yml:50`
- `.github/workflows/test.yml:53`
- `.github/workflows/test.yml:62`
- `.github/workflows/codeql.yml:13`
- `.github/workflows/codeql.yml:17`
- `.github/workflows/codeql.yml:20`
- `.github/workflows/codeql.yml:23`
- `.github/workflows/dependency-review.yml:12`
- `.github/workflows/dependency-review.yml:13`
- `.github/workflows/label-commenter.yml:18`
- `.github/workflows/label-commenter.yml:21`
- `.github/workflows/labeler.yml:13`
- `.github/workflows/labeler.yml:18`
- `.github/workflows/release.yml:10`
- `.github/workflows/release.yml:12`
- `.github/workflows/update-major-tag.yml:12`

### missing-permissions (severity: medium)

These workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any job, meaning they run with the default (potentially broad) token permissions: codeql.yml, release.yml, pages-status-check.yml, purge-readme-image-cache.yml, and update-major-tag.yml.

Locations:

- `.github/workflows/codeql.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/pages-status-check.yml:1`
- `.github/workflows/purge-readme-image-cache.yml:1`
- `.github/workflows/update-major-tag.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all findings across 9 workflow files:

1. script-injection (pages-status-check.yml): Moved github.event.build.status and github.event.build.error.message expressions to env: block as BUILD_STATUS and BUILD_ERROR_MESSAGE. Updated Python code to use os.environ[] to access them safely.

2. script-injection (update-major-tag.yml): Moved secrets.GITHUB_TOKEN expression to env: block as GITHUB_TOKEN. Script now references ${GITHUB_TOKEN} as a plain env var.

3. unpinned-uses: Pinned all 13 action references across test.yml, codeql.yml, dependency-review.yml, label-commenter.yml, labeler.yml, release.yml, and update-major-tag.yml to full 40-character SHA hashes with tag comments for readability.

4. missing-permissions: Added permissions blocks to codeql.yml (contents:read + security-events:write), release.yml (contents:write), pages-status-check.yml (contents:read), purge-readme-image-cache.yml (contents:read), and update-major-tag.yml (contents:write).

