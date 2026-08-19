<!-- markdownlint-disable -->

# Hardening Report: tj-actions--coverage-badge-py/v2.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **tj-actions--coverage-badge-py/v2.0.2** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in run: blocks. In action.yml, `${{ inputs.overwrite }}` is interpolated directly inside a bash `if` condition (single-quoting does not prevent injection since GitHub Actions substitutes the expression before the shell sees it), and `${{ inputs.output }}` is interpolated directly as a CLI argument to `coverage-badge`. An attacker-controlled calling workflow can inject arbitrary shell commands via these inputs.

Locations:

- `action.yml:27`
- `action.yml:31`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in a run: block. In test.yml, `${{ steps.verify-changed-files.outputs.changed_files }}` is interpolated directly into a `git add` shell command. The `changed_files` output could contain attacker-controlled filenames (e.g., from a PR branch) with shell metacharacters, enabling command injection.

Locations:

- `.github/workflows/test.yml:52`

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags or branch names instead of immutable 40-character SHA digests, making them vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved. Unpinned references found:
- auto-approve.yml: `hmarr/auto-approve-action@v3`
- auto-merge.yml: `pascalgn/automerge-action@v0.15.6`
- codacy-analysis.yml: `actions/checkout@v3`, `codacy/codacy-analysis-cli-action@v4.3.0`, `github/codeql-action/upload-sarif@v2`
- greetings.yml: `actions/first-interaction@v1`
- rebase.yml: `actions/checkout@v3`, `cirrus-actions/rebase@1.8`
- sync-release-version.yml: `actions/checkout@v3`, `tj-actions/release-tagger@v3`, `tj-actions/sync-release-version@v13`, `tj-actions/git-cliff@v1`, `peter-evans/create-pull-request@v5.0.0`
- test.yml: `actions/checkout@v3`, `actions/setup-python@v4`, `tj-actions/verify-changed-files@v14`, `ad-m/github-push-action@master`
- update-readme.yml: `actions/checkout@v3`, `tj-actions/auto-doc@v2`, `tj-actions/remark@v3`, `tj-actions/verify-changed-files@v14`, `peter-evans/create-pull-request@v5`

Locations:

- `.github/workflows/auto-approve.yml:9`
- `.github/workflows/auto-merge.yml:18`
- `.github/workflows/codacy-analysis.yml:30`
- `.github/workflows/codacy-analysis.yml:36`
- `.github/workflows/codacy-analysis.yml:48`
- `.github/workflows/greetings.yml:8`
- `.github/workflows/rebase.yml:10`
- `.github/workflows/rebase.yml:13`
- `.github/workflows/sync-release-version.yml:9`
- `.github/workflows/sync-release-version.yml:11`
- `.github/workflows/sync-release-version.yml:13`
- `.github/workflows/sync-release-version.yml:16`
- `.github/workflows/sync-release-version.yml:19`
- `.github/workflows/test.yml:21`
- `.github/workflows/test.yml:26`
- `.github/workflows/test.yml:44`
- `.github/workflows/test.yml:57`
- `.github/workflows/update-readme.yml:9`
- `.github/workflows/update-readme.yml:13`
- `.github/workflows/update-readme.yml:16`
- `.github/workflows/update-readme.yml:19`
- `.github/workflows/update-readme.yml:33`

### missing-permissions (severity: medium)

None of the 8 workflow files define a top-level `permissions:` key, and no job within any of these files defines job-level `permissions:` either. Without explicit permissions, workflows run with the default (often overly broad) token permissions, violating the principle of least privilege. Affected files: auto-approve.yml, auto-merge.yml, codacy-analysis.yml, greetings.yml, rebase.yml, sync-release-version.yml, test.yml, update-readme.yml.

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

Fixed all findings across action.yml and 8 workflow files:

1. action.yml script injection: Moved ${{ inputs.overwrite }} and ${{ inputs.output }} into env: block as INPUT_OVERWRITE and INPUT_OUTPUT, referenced as plain shell variables.

2. test.yml script injection: Moved ${{ steps.verify-changed-files.outputs.changed_files }} into env: block as CHANGED_FILES, referenced as "$CHANGED_FILES" in git add command.

3. Unpinned actions: Pinned all 22 action references across 8 workflow files to full 40-char SHA digests with original tag as comment:
   - hmarr/auto-approve-action@v3 → 7d0ab8fdbb906da8a6297d373561d5ccb137d98f
   - pascalgn/automerge-action@v0.15.6 → 22948e0bc22f0aa673800da838595a3e7347e584
   - actions/checkout@v3 → a37ce9120846195fa4ece8f58b268e6043cb2f26
   - codacy/codacy-analysis-cli-action@v4.3.0 → 5cc54a75f9ad88159bb54046196d920e40e367a5
   - github/codeql-action/upload-sarif@v2 → b8d3b6e8af63cde30bdc382c0bc28114f4346c88
   - actions/first-interaction@v1 → 2ec0f0fd78838633cd1c1342e4536d49ef72be54
   - cirrus-actions/rebase@1.8 → b87d48154a87a85666003575337e27b8cd65f691
   - tj-actions/release-tagger@v3 → 6bb53bc882f703cea0839d1fbf1603b61d25f7e0
   - tj-actions/sync-release-version@v13 → 2a7ef0deb39b3ecce887ee99d2261c6cef989d84
   - tj-actions/git-cliff@v1 → 75599f745633e29f99bd9e14a30865b7d2fcbe84
   - peter-evans/create-pull-request@v5.0.0 → 5b4a9f6a9e2af26e5f02351490b90d01eb8ec1e5
   - actions/setup-python@v4 → 7f4fc3e22c37d6ff65e88745f38bd3157c663f7c
   - tj-actions/verify-changed-files@v14 → 7517b838f3a0d51de4b334a61ef1330672118927
   - ad-m/github-push-action@master → 881a6320fdb16eb5318c5054f31c218aec2b324c
   - tj-actions/auto-doc@v2 → a84f60895ff9609bb13c7ad43b19cd69cfc4b567
   - tj-actions/remark@v3 → 10fc40701928cbafcc4a2d241679579d218144ff
   - peter-evans/create-pull-request@v5 → 4e1beaa7521e8b457b572c090b25bd3db56bf1c5

4. Missing permissions: Added minimal permissions blocks to all 8 workflow files.

