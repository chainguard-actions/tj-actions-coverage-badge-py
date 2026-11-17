<!-- markdownlint-disable -->

# Hardening Report: tj-actions--coverage-badge-py/v2.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **tj-actions--coverage-badge-py/v2.0.4** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): GitHub Actions expressions are put straight into the run: script of the composite step 'coverage-badge'. Line 27: `if [[ '${{ inputs.overwrite }}' == 'true'  ]]; then`. Line 31: `coverage-badge $EXTRA_ARGS -o "${{ inputs.output }}"`. The input values are pasted into the script before bash parses it, so a caller can inject shell commands by putting quotes or $(...) in them. Fix: map inputs.overwrite and inputs.output to env: variables (for example INPUT_OVERWRITE and INPUT_OUTPUT) and use them double-quoted ("$INPUT_OVERWRITE", "$INPUT_OUTPUT").

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 7432739..6214d12 100644
--- a/action.yml
+++ b/action.yml
@@ -14,21 +14,40 @@ inputs:
     description: 'Current working directory'
     required: false
     default: '.'
-
 runs:
   using: 'composite'
   steps:
     - id: coverage-badge
       working-directory: ${{ inputs.working-directory }}
+      env:
+        INPUT_OVERWRITE: ${{ inputs.overwrite }}
+        INPUT_OUTPUT: ${{ inputs.output }}
       run: |
         pip install -U setuptools coverage-badge
         EXTRA_ARGS=""
 
-        if [[ '${{ inputs.overwrite }}' == 'true'  ]]; then
+        if [[ "$INPUT_OVERWRITE" == 'true'  ]]; then
           EXTRA_ARGS+="-f"
         fi
 
-        coverage-badge $EXTRA_ARGS -o "${{ inputs.output }}"
+        coverage-badge $EXTRA_ARGS -o "$INPUT_OUTPUT"
       shell: bash
 branding:
   icon: check-circle

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, prior-fix

**Notes:**

Moved inputs.overwrite and inputs.output into env vars (INPUT_OVERWRITE, INPUT_OUTPUT) on the coverage-badge step and referenced them double-quoted in the bash script; re-applies the prior harden's patch. EXTRA_ARGS left unquoted as upstream (internal literal, not injectable).

