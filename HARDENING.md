<!-- markdownlint-disable -->

# Hardening Report: losisin--helm-values-schema-json-action/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **losisin--helm-values-schema-json-action/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable version tags instead of pinned full-length SHA commit hashes, making them vulnerable to supply-chain attacks if the tag is moved.

.github/workflows/check-dist.yaml:
  - actions/checkout@v6 (line 26)
  - actions/setup-node@v6 (line 29)
  - actions/upload-artifact@v7 (line 53)

.github/workflows/ci.yaml:
  - actions/checkout@v6 (line 20)
  - actions/setup-node@v6 (line 24)
  - codecov/codecov-action@v6 (line 42)
  - actions/checkout@v6 (line 55)

.github/workflows/codeql-analysis.yaml:
  - actions/checkout@v6 (line 28)
  - github/codeql-action/init@v4 (line 32)
  - github/codeql-action/autobuild@v4 (line 40)
  - github/codeql-action/analyze@v4 (line 44)

Locations:

- `.github/workflows/check-dist.yaml:26`
- `.github/workflows/check-dist.yaml:29`
- `.github/workflows/check-dist.yaml:53`
- `.github/workflows/ci.yaml:20`
- `.github/workflows/ci.yaml:24`
- `.github/workflows/ci.yaml:42`
- `.github/workflows/ci.yaml:55`
- `.github/workflows/codeql-analysis.yaml:28`
- `.github/workflows/codeql-analysis.yaml:32`
- `.github/workflows/codeql-analysis.yaml:40`
- `.github/workflows/codeql-analysis.yaml:44`

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is interpolated directly inside a run: shell command. In ci.yaml, the step 'Print Cached Path' runs: `echo "${{ steps.test-action.outputs.plugin-path }}"`. The expression ${{ steps.test-action.outputs.plugin-path }} is substituted into the shell command string before the shell parses it, allowing any newlines or shell metacharacters in the output value to be interpreted by the shell. This should be rewritten to pass the value through an environment variable: `env: PLUGIN_PATH: ${{ steps.test-action.outputs.plugin-path }}` and then `run: echo "$PLUGIN_PATH"`.

Locations:

- `.github/workflows/ci.yaml:72`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Pinned all 11 unpinned action references to full commit SHAs: actions/checkout@v6 → d23441a48e516b6c34aea4fa41551a30e30af803, actions/setup-node@v6 → 249970729cb0ef3589644e2896645e5dc5ba9c38, actions/upload-artifact@v7 → 043fb46d1a93c77aae656e7c1c64a875d1fc6a0a, codecov/codecov-action@v6 → fb8b3582c8e4def4969c97caa2f19720cb33a72f, github/codeql-action/{init,autobuild,analyze}@v4 → e4fba868fa4b1b91e1fdab776edc8cfbe6e9fb81. Fixed script-injection in ci.yaml by moving ${{ steps.test-action.outputs.plugin-path }} into an env block as PLUGIN_PATH and referencing it as $PLUGIN_PATH in the shell command.

