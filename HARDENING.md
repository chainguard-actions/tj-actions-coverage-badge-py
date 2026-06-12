<!-- markdownlint-disable -->

# Hardening Report: tj-actions--coverage-badge-py/v2.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **tj-actions--coverage-badge-py/v2.0.4** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The run: block in action.yml directly interpolates ${{ inputs.overwrite }} and ${{ inputs.output }} inside shell commands (rule a). These expressions are substituted by the Actions runner before the shell parses the command, allowing an attacker who controls the calling workflow's inputs to inject arbitrary shell commands.

Offending lines:
  if [[ '${{ inputs.overwrite }}' == 'true'  ]]; then   # inputs.overwrite interpolated directly in shell
  coverage-badge $EXTRA_ARGS -o "${{ inputs.output }}"   # inputs.output interpolated directly in shell

Fix: move the values into env: variables and reference them as quoted shell variables, e.g.:
  env:
    OVERWRITE: ${{ inputs.overwrite }}
    OUTPUT: ${{ inputs.output }}
  run: |
    if [[ "$OVERWRITE" == 'true' ]]; then
      EXTRA_ARGS+="-f"
    fi
    coverage-badge $EXTRA_ARGS -o "$OUTPUT"

Locations:

- `action.yml:27`
- `action.yml:31`

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

Fixed script injection in action.yml by adding an env: block to the coverage-badge step with OVERWRITE=${{ inputs.overwrite }} and OUTPUT=${{ inputs.output }}. Updated the run: block to reference $OVERWRITE and $OUTPUT as plain shell variables instead of directly interpolating ${{ inputs.overwrite }} and ${{ inputs.output }} in the shell commands. This prevents attacker-controlled input values from being interpreted as shell commands.

