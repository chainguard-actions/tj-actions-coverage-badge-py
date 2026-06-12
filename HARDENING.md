<!-- markdownlint-disable -->

# Hardening Report: tj-actions--coverage-badge-py/v2.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **tj-actions--coverage-badge-py/v2.0.2** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in action.yml directly interpolates `${{ inputs.overwrite }}` inside a shell `if` condition: `if [[ '${{ inputs.overwrite }}' == 'true' ]]; then`. Although the value appears single-quoted in the YAML source, GitHub Actions performs expression substitution *before* the shell parses the script, so an attacker-controlled value can break out of the single-quote context and inject arbitrary shell commands. The value should be passed via an `env:` variable and referenced as a quoted shell variable (e.g., `"$OVERWRITE"`) instead.

Locations:

- `action.yml:23`

### script-injection (severity: high)

Sub-rule (a): The `run:` block in action.yml directly interpolates `${{ inputs.output }}` as a shell argument: `coverage-badge $EXTRA_ARGS -o "${{ inputs.output }}"`. GitHub Actions substitutes the expression before the shell executes the script, allowing an attacker-controlled `output` input to inject arbitrary shell commands (e.g., a value like `foo; malicious-command`). The value should be passed via an `env:` variable and referenced as a quoted shell variable (e.g., `-o "$OUTPUT"`) instead.

Locations:

- `action.yml:27`

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

Fixed all four findings in action.yml by adding an `env:` block to the coverage-badge step with OVERWRITE=${{ inputs.overwrite }} and OUTPUT=${{ inputs.output }}. Updated the run: block to reference these as shell variables: `if [[ "$OVERWRITE" == 'true' ]]` and `coverage-badge $EXTRA_ARGS -o "$OUTPUT"`. This prevents GitHub Actions expression substitution from allowing attacker-controlled values to inject arbitrary shell commands.

