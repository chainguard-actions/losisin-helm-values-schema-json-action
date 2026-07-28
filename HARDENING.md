<!-- markdownlint-disable -->

# Hardening Report: losisin--helm-values-schema-json-action/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **losisin--helm-values-schema-json-action/v3.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All three workflow files reference GitHub Actions using mutable version tags (e.g., @v7, @v4) instead of full 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if a tag is moved or a dependency is compromised. Affected references include: actions/checkout@v7, actions/setup-node@v7, actions/upload-artifact@v7, codecov/codecov-action@v7, github/codeql-action/init@v4, github/codeql-action/autobuild@v4, github/codeql-action/analyze@v4.

Locations:

- `.github/workflows/check-dist.yaml:22`
- `.github/workflows/check-dist.yaml:27`
- `.github/workflows/check-dist.yaml:47`
- `.github/workflows/ci.yaml:18`
- `.github/workflows/ci.yaml:24`
- `.github/workflows/ci.yaml:40`
- `.github/workflows/ci.yaml:51`
- `.github/workflows/codeql-analysis.yaml:24`
- `.github/workflows/codeql-analysis.yaml:30`
- `.github/workflows/codeql-analysis.yaml:40`
- `.github/workflows/codeql-analysis.yaml:44`

### script-injection (severity: high)

Rule (a) violation: A ${{ }} expression is interpolated directly inside a run: shell command string. In ci.yaml, the step 'Print Cached Path' uses `run: echo "${{ steps.test-action.outputs.plugin-path }}"`. The steps.*.outputs.* context is workflow-controllable and flows through YAML template substitution before the shell sees it, enabling script injection. The value should be passed via an env: variable and then referenced as a quoted shell variable (e.g., `echo "$PLUGIN_PATH"`).

Locations:

- `.github/workflows/ci.yaml:52`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Pinned all action references to full SHA hashes: actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1, actions/setup-node@v7 → @820762786026740c76f36085b0efc47a31fe5020, actions/upload-artifact@v7 → @043fb46d1a93c77aae656e7c1c64a875d1fc6a0a, codecov/codecov-action@v7 → @fb8b3582c8e4def4969c97caa2f19720cb33a72f, github/codeql-action/{init,autobuild,analyze}@v4 → @e4fba868fa4b1b91e1fdab776edc8cfbe6e9fb81. Fixed script injection in ci.yaml by moving ${{ steps.test-action.outputs.plugin-path }} into an env: block as PLUGIN_PATH and referencing it as $PLUGIN_PATH in the shell command.

