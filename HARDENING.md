<!-- markdownlint-disable -->

# Hardening Report: peaceiris--actions-gh-pages/v4.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **peaceiris--actions-gh-pages/v4.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): GitHub Actions expressions are directly interpolated inside a `run:` block. In pages-status-check.yml, `${{ github.event.build.status }}` (line 12) and `${{ github.event.build.error.message }}` (line 13) are injected directly into a Python shell script. These values come from the `page_build` event payload and could be attacker-influenced, allowing arbitrary Python code execution. The values must be passed via environment variables and referenced as `os.environ` instead.

Locations:

- `.github/workflows/pages-status-check.yml:12`
- `.github/workflows/pages-status-check.yml:13`

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression `${{ secrets.GITHUB_TOKEN }}` is directly interpolated inside a `run:` shell command string in update-major-tag.yml (line 18): `git remote set-url origin "https://x-access-token:${{ secrets.GITHUB_TOKEN }}@github.com/${GITHUB_REPOSITORY}.git"`. Any `${{ ... }}` expression directly inside a `run:` block is a script-injection risk as the value is substituted into the shell command string before the shell parses it. The token should be passed via an environment variable instead.

Locations:

- `.github/workflows/update-major-tag.yml:18`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script-injection findings:
1. hardened/action/.github/workflows/pages-status-check.yml: Moved `${{ github.event.build.status }}` and `${{ github.event.build.error.message }}` from direct interpolation inside the Python `run:` block into an `env:` block (as BUILD_STATUS and BUILD_ERROR_MESSAGE). The Python script now reads them safely via `os.environ` instead of having attacker-controlled values interpolated as string literals.
2. hardened/action/.github/workflows/update-major-tag.yml: Moved `${{ secrets.GITHUB_TOKEN }}` from direct interpolation inside the shell `run:` block into an `env:` block (as GH_TOKEN). The shell script now references it as `${GH_TOKEN}` — a plain environment variable — instead of having it substituted directly into the command string before shell parsing.

