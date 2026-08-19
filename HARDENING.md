<!-- markdownlint-disable -->

# Hardening Report: losisin--helm-values-schema-json-action/v2.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **losisin--helm-values-schema-json-action/v2.4.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable version tags (e.g. @v4, @v5, @v6) instead of pinned full 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if a tag is moved to point to malicious code.

check-dist.yaml: actions/checkout@v6 (line 26), actions/setup-node@v6 (line 30), actions/upload-artifact@v6 (line 52).
ci.yaml: actions/checkout@v6 (line 20), actions/setup-node@v6 (line 24), codecov/codecov-action@v5 (line 43), actions/checkout@v6 (line 57).
codeql-analysis.yaml: actions/checkout@v6 (line 27), github/codeql-action/init@v4 (line 31), github/codeql-action/autobuild@v4 (line 37), github/codeql-action/analyze@v4 (line 41).

Locations:

- `.github/workflows/check-dist.yaml:26`
- `.github/workflows/check-dist.yaml:30`
- `.github/workflows/check-dist.yaml:52`
- `.github/workflows/ci.yaml:20`
- `.github/workflows/ci.yaml:24`
- `.github/workflows/ci.yaml:43`
- `.github/workflows/ci.yaml:57`
- `.github/workflows/codeql-analysis.yaml:27`
- `.github/workflows/codeql-analysis.yaml:31`
- `.github/workflows/codeql-analysis.yaml:37`
- `.github/workflows/codeql-analysis.yaml:41`

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression is directly interpolated inside a run: shell command. In ci.yaml line 70, the step 'Print Cached Path' contains: `run: echo "${{ steps.test-action.outputs.plugin-path }}"`. The expression ${{ steps.test-action.outputs.plugin-path }} is substituted into the shell command string before the shell parses it, allowing an attacker who can control the action output to inject arbitrary shell commands. The value should be passed via an env: variable and then referenced as a quoted shell variable (e.g. `echo "$PLUGIN_PATH"`) instead.

Locations:

- `.github/workflows/ci.yaml:70`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 11 unpinned action references across 3 workflow files by pinning to full SHA hashes: actions/checkout@v6 → d23441a48e516b6c34aea4fa41551a30e30af803, actions/setup-node@v6 → 249970729cb0ef3589644e2896645e5dc5ba9c38, actions/upload-artifact@v6 → b7c566a772e6b6bfb58ed0dc250532a479d7789f, codecov/codecov-action@v5 → 0fb7174895f61a3b6b78fc075e0cd60383518dac, github/codeql-action/{init,autobuild,analyze}@v4 → e4fba868fa4b1b91e1fdab776edc8cfbe6e9fb81. Fixed script injection in ci.yaml by moving ${{ steps.test-action.outputs.plugin-path }} into an env: block as PLUGIN_PATH and referencing it as $PLUGIN_PATH in the shell command.

