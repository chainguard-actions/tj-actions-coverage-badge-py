<!-- markdownlint-disable -->

# Hardening Report: tj-actions--coverage-badge-py/v2.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **tj-actions--coverage-badge-py/v2.0.3** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in action.yml directly interpolates GitHub Actions expressions inside shell commands without routing through env vars. Two occurrences:
1. Line 26: `if [[ '${{ inputs.overwrite }}' == 'true'  ]]; then` — `inputs.overwrite` is interpolated directly into a shell `if` condition. A caller can supply a value containing shell metacharacters (e.g., `'; malicious_cmd; '`) to achieve command injection.
2. Line 30: `coverage-badge $EXTRA_ARGS -o "${{ inputs.output }}"` — `inputs.output` is interpolated directly as a shell argument. A caller can supply a value like `foo; malicious_cmd` to inject arbitrary commands.
Fix: move both inputs into `env:` variables and reference them as quoted shell variables (e.g., `"$INPUT_OUTPUT"`).

Locations:

- `action.yml:26`
- `action.yml:30`

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

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Fixed script injection in action.yml by adding an `env:` block to the coverage-badge step with INPUT_OVERWRITE and INPUT_OUTPUT variables. Replaced direct `${{ inputs.overwrite }}` and `${{ inputs.output }}` interpolations in the run: shell script with `"$INPUT_OVERWRITE"` and `"$INPUT_OUTPUT"` references. This prevents callers from injecting shell metacharacters through these inputs.

