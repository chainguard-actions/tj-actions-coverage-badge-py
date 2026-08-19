<!-- markdownlint-disable -->

# Hardening Report: tj-actions--coverage-badge-py/v2.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **tj-actions--coverage-badge-py/v2.0.1** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

In action.yml, the composite action's run: block directly interpolates ${{ inputs.overwrite }} (line 26) and ${{ inputs.output }} (line 30) into shell commands. These inputs are attacker-controllable and flow through YAML template substitution before the shell processes them, enabling command injection. For example: `if [[ '${{ inputs.overwrite }}' == 'true' ]]; then` and `coverage-badge $EXTRA_ARGS -o "${{ inputs.output }}"`.

Locations:

- `action.yml:26`
- `action.yml:30`

### script-injection (severity: high)

In .github/workflows/test.yml, the 'Commit README changes' step directly interpolates ${{ steps.verify-changed-files.outputs.changed_files }} into a run: shell command without quoting (line 54): `git add ${{ steps.verify-changed-files.outputs.changed_files }}`. This violates both sub-rule (a) — direct expression interpolation in a run: block — and sub-rule (b) — the value is unquoted, allowing shell metacharacter injection.

Locations:

- `.github/workflows/test.yml:54`

### unpinned-uses (severity: high)

All uses: references across every workflow file use mutable tags or version strings instead of full 40-character SHA commit hashes, making the workflows vulnerable to supply-chain attacks. Failing references include: auto-approve.yml: hmarr/auto-approve-action@v3; auto-merge.yml: pascalgn/automerge-action@v0.15.6; codacy-analysis.yml: actions/checkout@v3, codacy/codacy-analysis-cli-action@v4.2.0, github/codeql-action/upload-sarif@v2; greetings.yml: actions/first-interaction@v1; rebase.yml: actions/checkout@v3, cirrus-actions/rebase@1.8; sync-release-version.yml: actions/checkout@v3, tj-actions/release-tagger@v2, tj-actions/sync-release-version@v13, tj-actions/github-changelog-generator@v1.17, peter-evans/create-pull-request@v4.2.3; test.yml: actions/checkout@v3, actions/setup-python@v4, tj-actions/verify-changed-files@v13, ad-m/github-push-action@master; update-readme.yml: actions/checkout@v3, tj-actions/auto-doc@v1, tj-actions/remark@v3, tj-actions/verify-changed-files@v13, peter-evans/create-pull-request@v4.

Locations:

- `.github/workflows/auto-approve.yml:9`
- `.github/workflows/auto-merge.yml:17`
- `.github/workflows/codacy-analysis.yml:30`
- `.github/workflows/codacy-analysis.yml:37`
- `.github/workflows/codacy-analysis.yml:50`
- `.github/workflows/greetings.yml:8`
- `.github/workflows/rebase.yml:11`
- `.github/workflows/rebase.yml:14`
- `.github/workflows/sync-release-version.yml:9`
- `.github/workflows/sync-release-version.yml:11`
- `.github/workflows/sync-release-version.yml:13`
- `.github/workflows/sync-release-version.yml:18`
- `.github/workflows/sync-release-version.yml:22`
- `.github/workflows/test.yml:22`
- `.github/workflows/test.yml:27`
- `.github/workflows/test.yml:46`
- `.github/workflows/test.yml:58`
- `.github/workflows/update-readme.yml:11`
- `.github/workflows/update-readme.yml:14`
- `.github/workflows/update-readme.yml:17`
- `.github/workflows/update-readme.yml:20`
- `.github/workflows/update-readme.yml:29`

### missing-permissions (severity: medium)

None of the 8 workflow files under .github/workflows/ define a top-level permissions: key, and no individual job within any of these workflows defines a job-level permissions: key. This means all jobs run with the default broad permissions granted by GitHub Actions, violating the principle of least privilege.

Locations:

- `.github/workflows/auto-approve.yml:1`
- `.github/workflows/auto-merge.yml:1`
- `.github/workflows/codacy-analysis.yml:1`
- `.github/workflows/greetings.yml:1`
- `.github/workflows/rebase.yml:1`
- `.github/workflows/sync-release-version.yml:1`
- `.github/workflows/test.yml:1`
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

Fixed all findings: (1) action.yml script injection: moved inputs.overwrite and inputs.output from run: block into env: block as INPUT_OVERWRITE and INPUT_OUTPUT; (2) test.yml script injection: moved steps.verify-changed-files.outputs.changed_files into env: block as CHANGED_FILES; (3) Pinned all 22 action uses: references across 8 workflow files to full 40-char SHA hashes with original tags as comments; (4) Added minimal permissions: blocks to all 8 workflow files.

