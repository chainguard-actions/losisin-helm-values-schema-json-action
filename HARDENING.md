<!-- markdownlint-disable -->

# Hardening Report: losisin--helm-values-schema-json-action/v3.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **losisin--helm-values-schema-json-action/v3.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: A `${{ ... }}` expression is directly interpolated inside a `run:` shell command. In `.github/workflows/ci.yaml`, the step 'Print Cached Path' contains `run: echo "${{ steps.test-action.outputs.plugin-path }}"`. The value of `steps.test-action.outputs.plugin-path` is substituted into the shell command string before the shell ever sees it, allowing an attacker who can influence that output value to inject arbitrary shell commands.

Locations:

- `.github/workflows/ci.yaml:63`

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tag refs instead of pinned 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the tag is moved. Failing references:

`.github/workflows/ci.yaml`:
- `actions/checkout@v6`
- `actions/setup-node@v6`
- `codecov/codecov-action@v7`

`.github/workflows/check-dist.yaml`:
- `actions/checkout@v6`
- `actions/setup-node@v6`
- `actions/upload-artifact@v7`

`.github/workflows/codeql-analysis.yaml`:
- `actions/checkout@v6`
- `github/codeql-action/init@v4`
- `github/codeql-action/autobuild@v4`
- `github/codeql-action/analyze@v4`

Locations:

- `.github/workflows/ci.yaml:19`
- `.github/workflows/ci.yaml:24`
- `.github/workflows/ci.yaml:36`
- `.github/workflows/check-dist.yaml:24`
- `.github/workflows/check-dist.yaml:29`
- `.github/workflows/check-dist.yaml:52`
- `.github/workflows/codeql-analysis.yaml:22`
- `.github/workflows/codeql-analysis.yaml:27`
- `.github/workflows/codeql-analysis.yaml:37`
- `.github/workflows/codeql-analysis.yaml:42`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection in ci.yaml by moving `${{ steps.test-action.outputs.plugin-path }}` into an env block (PLUGIN_PATH) and referencing it as $PLUGIN_PATH in the shell command. Pinned all unpinned action references across ci.yaml, check-dist.yaml, and codeql-analysis.yaml to full 40-character SHA commit hashes: actions/checkout@v6→df4cb1c, actions/setup-node@v6→249970729, codecov/codecov-action@v7→fb8b3582, actions/upload-artifact@v7→043fb46d, github/codeql-action/{init,autobuild,analyze}@v4→7188fc36. Original tags preserved as inline comments.

