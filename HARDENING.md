<!-- markdownlint-disable -->

# Hardening Report: tj-actions--coverage-badge-py/v2.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **tj-actions--coverage-badge-py/v2.0.4** was hardened automatically. 10 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of untrusted inputs inside run: shell commands. In action.yml, line 25 interpolates `${{ inputs.overwrite }}` directly inside a shell conditional (single-quote wrapping does NOT prevent injection — the substitution occurs before the shell sees the string, allowing an attacker to break out of the quotes). Line 29 interpolates `${{ inputs.output }}` directly into the coverage-badge command argument. Both allow arbitrary shell command injection via the calling workflow's inputs.

Locations:

- `action.yml:25`
- `action.yml:29`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of a steps output context value inside a run: shell command. In test.yml, line 53 uses `git add ${{ steps.verify-changed-files.outputs.changed_files }}` — the steps output is interpolated directly into the shell command, allowing injection of arbitrary shell metacharacters via the output value.

Locations:

- `.github/workflows/test.yml:53`

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags or branch names instead of full 40-character commit SHAs. Failing references: `actions/checkout@v4`, `codacy/codacy-analysis-cli-action@v4.4.5`, `github/codeql-action/upload-sarif@v3`.

Locations:

- `.github/workflows/codacy-analysis.yml:28`
- `.github/workflows/codacy-analysis.yml:33`
- `.github/workflows/codacy-analysis.yml:55`

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags or branch names instead of full 40-character commit SHAs. Failing references: `actions/checkout@v4`, `actions/setup-python@v5`, `tj-actions/verify-changed-files@v20`, `ad-m/github-push-action@master` (branch name — especially dangerous).

Locations:

- `.github/workflows/test.yml:21`
- `.github/workflows/test.yml:26`
- `.github/workflows/test.yml:44`
- `.github/workflows/test.yml:58`

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable tags instead of full 40-character commit SHAs. Failing references: `actions/checkout@v4`, `tj-actions/auto-doc@v3`, `tj-actions/remark@v3`, `tj-actions/verify-changed-files@v20`, `peter-evans/create-pull-request@v7`.

Locations:

- `.github/workflows/update-readme.yml:12`
- `.github/workflows/update-readme.yml:15`
- `.github/workflows/update-readme.yml:20`
- `.github/workflows/update-readme.yml:23`
- `.github/workflows/update-readme.yml:35`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions (which may be write-all), violating the principle of least privilege.

Locations:

- `.github/workflows/codacy-analysis.yml:1`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/test.yml:1`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/update-readme.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.overwrite }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:27`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.output }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:31`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all findings across action.yml and three workflow files:

1. action.yml: Moved ${{ inputs.overwrite }} and ${{ inputs.output }} from run: shell block into env: map (INPUT_OVERWRITE, INPUT_OUTPUT), referencing them as plain env vars in the shell script.

2. test.yml: Moved ${{ steps.verify-changed-files.outputs.changed_files }} into env: block as CHANGED_FILES; pinned all 4 unpinned actions to full commit SHAs; added top-level permissions: {contents: read}.

3. codacy-analysis.yml: Pinned all 3 unpinned actions to full commit SHAs; added top-level permissions: {contents: read, security-events: write} (security-events: write required for SARIF upload).

4. update-readme.yml: Pinned all 5 unpinned actions to full commit SHAs; added top-level permissions: {contents: read, pull-requests: write} (pull-requests: write required for create-pull-request action).

