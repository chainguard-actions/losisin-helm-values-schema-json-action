<!-- markdownlint-disable -->

# Hardening Report: losisin--helm-values-schema-json-action/v3.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **losisin--helm-values-schema-json-action/v3.2.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable version tags instead of full 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if a tag is moved or a repository is compromised.

Failing references:
- .github/workflows/ci.yaml: actions/checkout@v7, actions/setup-node@v7, codecov/codecov-action@v7
- .github/workflows/check-dist.yaml: actions/checkout@v7, actions/setup-node@v7, actions/upload-artifact@v7
- .github/workflows/codeql-analysis.yaml: actions/checkout@v7, github/codeql-action/init@v4, github/codeql-action/autobuild@v4, github/codeql-action/analyze@v4

Locations:

- `.github/workflows/ci.yaml:19`
- `.github/workflows/ci.yaml:24`
- `.github/workflows/ci.yaml:38`
- `.github/workflows/ci.yaml:50`
- `.github/workflows/check-dist.yaml:23`
- `.github/workflows/check-dist.yaml:27`
- `.github/workflows/check-dist.yaml:52`
- `.github/workflows/codeql-analysis.yaml:22`
- `.github/workflows/codeql-analysis.yaml:27`
- `.github/workflows/codeql-analysis.yaml:33`
- `.github/workflows/codeql-analysis.yaml:38`

### script-injection (severity: high)

Rule (a) violation: The 'Print Cached Path' step in ci.yaml directly interpolates a GitHub Actions expression inside a run: shell command: `run: echo "${{ steps.test-action.outputs.plugin-path }}"`.

The value of steps.test-action.outputs.plugin-path flows through YAML template substitution before the shell processes it. If this output contains shell metacharacters (e.g. from a malicious input or compromised action), it could result in arbitrary command execution. The value should be passed via an env: variable and then referenced as a quoted shell variable instead.

Locations:

- `.github/workflows/ci.yaml:62`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all three workflow files:

1. ci.yaml: Pinned actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1, actions/setup-node@v7 → @820762786026740c76f36085b0efc47a31fe5020, codecov/codecov-action@v7 → @fb8b3582c8e4def4969c97caa2f19720cb33a72f (both occurrences of checkout). Also fixed script injection in 'Print Cached Path' step by moving ${{ steps.test-action.outputs.plugin-path }} into an env: block as PLUGIN_PATH and referencing it as "$PLUGIN_PATH" in the shell command.

2. check-dist.yaml: Pinned actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1, actions/setup-node@v7 → @820762786026740c76f36085b0efc47a31fe5020, actions/upload-artifact@v7 → @043fb46d1a93c77aae656e7c1c64a875d1fc6a0a.

3. codeql-analysis.yaml: Pinned actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1, github/codeql-action/init@v4 → @cdf488f595d80d6e07e03d4674febd5ab45fa938, github/codeql-action/autobuild@v4 → @cdf488f595d80d6e07e03d4674febd5ab45fa938, github/codeql-action/analyze@v4 → @cdf488f595d80d6e07e03d4674febd5ab45fa938. All original tags preserved as inline comments.

