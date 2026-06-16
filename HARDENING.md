<!-- markdownlint-disable -->

# Hardening Report: tj-actions--coverage-badge-py/v2.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **tj-actions--coverage-badge-py/v2.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Two `${{ inputs.* }}` expressions are interpolated directly inside a `run:` shell command block in action.yml.

1. Line 25: `if [[ '${{ inputs.overwrite }}' == 'true'  ]]; then` — an attacker-controlled value for `inputs.overwrite` is injected into the shell condition. Despite the surrounding single quotes, an attacker can supply a value like `' ]] && malicious_command #` to break out and execute arbitrary commands.

2. Line 29: `coverage-badge $EXTRA_ARGS -o "${{ inputs.output }}"` — an attacker-controlled value for `inputs.output` is injected into the shell command as a CLI argument. An attacker can supply a value like `foo" && malicious_command #` to execute arbitrary commands.

Fix: Move both inputs into `env:` variables and reference them as properly double-quoted shell variables (e.g., `"$INPUT_OVERWRITE"`, `"$INPUT_OUTPUT"`) — never interpolate `${{ ... }}` directly inside a `run:` block.

Locations:

- `action.yml:25`
- `action.yml:29`

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

Fixed script injection vulnerabilities in action.yml by adding an env: block to the coverage-badge step with INPUT_OVERWRITE and INPUT_OUTPUT variables mapped from ${{ inputs.overwrite }} and ${{ inputs.output }} respectively. Updated the run: block to reference these as shell variables ("$INPUT_OVERWRITE" and "$INPUT_OUTPUT") instead of interpolating the ${{ }} expressions directly in the shell script. This prevents attacker-controlled input values from breaking out of their context and executing arbitrary commands.

