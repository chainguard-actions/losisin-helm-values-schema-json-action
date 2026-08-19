<!-- markdownlint-disable -->

# Hardening Report: losisin--helm-values-schema-json-action/v2.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **losisin--helm-values-schema-json-action/v2.5.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable version tags instead of full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the tag is moved.

.github/workflows/check-dist.yaml:
  - uses: actions/checkout@v6 (line 26)
  - uses: actions/setup-node@v6 (line 29)
  - uses: actions/upload-artifact@v7 (line 52)

.github/workflows/ci.yaml:
  - uses: actions/checkout@v6 (line 14)
  - uses: actions/setup-node@v6 (line 19)
  - uses: codecov/codecov-action@v5 (line 38)
  - uses: actions/checkout@v6 (line 53)

.github/workflows/codeql-analysis.yaml:
  - uses: actions/checkout@v6 (line 27)
  - uses: github/codeql-action/init@v4 (line 31)
  - uses: github/codeql-action/autobuild@v4 (line 37)
  - uses: github/codeql-action/analyze@v4 (line 41)

All should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/check-dist.yaml:26`
- `.github/workflows/check-dist.yaml:29`
- `.github/workflows/check-dist.yaml:52`
- `.github/workflows/ci.yaml:14`
- `.github/workflows/ci.yaml:19`
- `.github/workflows/ci.yaml:38`
- `.github/workflows/ci.yaml:53`
- `.github/workflows/codeql-analysis.yaml:27`
- `.github/workflows/codeql-analysis.yaml:31`
- `.github/workflows/codeql-analysis.yaml:37`
- `.github/workflows/codeql-analysis.yaml:41`

### script-injection (severity: high)

Rule (a) violation: A ${{ }} expression is interpolated directly inside a `run:` shell command string. In ci.yaml, the 'Print Cached Path' step uses:

  run: echo "${{ steps.test-action.outputs.plugin-path }}"

The value of `steps.test-action.outputs.plugin-path` flows through YAML template substitution before the shell sees it, allowing an attacker who can influence the action's output to inject arbitrary shell commands. The fix is to pass the value via an `env:` variable and reference it as a quoted shell variable:

  env:
    PLUGIN_PATH: ${{ steps.test-action.outputs.plugin-path }}
  run: echo "$PLUGIN_PATH"

Locations:

- `.github/workflows/ci.yaml:63`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 11 unpinned action references across 3 workflow files by pinning to full SHA hashes: actions/checkout@v6 → df4cb1c069e1874edd31b4311f1884172cec0e10, actions/setup-node@v6 → 249970729cb0ef3589644e2896645e5dc5ba9c38, actions/upload-artifact@v7 → 043fb46d1a93c77aae656e7c1c64a875d1fc6a0a, codecov/codecov-action@v5 → 0fb7174895f61a3b6b78fc075e0cd60383518dac, github/codeql-action/{init,autobuild,analyze}@v4 → 7188fc363630916deb702c7fdcf4e481b751f97a. Fixed script injection in ci.yaml 'Print Cached Path' step by moving ${{ steps.test-action.outputs.plugin-path }} into an env: block as PLUGIN_PATH and referencing it as $PLUGIN_PATH in the shell command.

