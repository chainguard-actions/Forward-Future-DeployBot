<!-- markdownlint-disable -->

# Hardening Report: Forward-Future--DeployBot/v0.2.15

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Forward-Future--DeployBot/v0.2.15** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) is violated: a GitHub Actions expression is placed directly inside a run: script. The offending line is `run: python -m pip install "${{ github.action_path }}"`. Fix it by moving the value into an env: variable (e.g. ACTION_PATH: ${{ github.action_path }}) or using the runner-provided $GITHUB_ACTION_PATH, and quote the expansion: python -m pip install "$GITHUB_ACTION_PATH".

Locations:

- `action.yml:31`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 70341cf..9e5e246 100644
--- a/action.yml
+++ b/action.yml
@@ -24,11 +24,30 @@ inputs:
 runs:
   using: composite
   steps:
     - uses: actions/setup-python@a309ff8b426b58ec0e2a45f0f869d46889d02405
       with:
         python-version: "3.11"
     - shell: bash
-      run: python -m pip install "${{ github.action_path }}"
+      env:
+        ACTION_PATH: ${{ github.action_path }}
+      run: python -m pip install "$ACTION_PATH"
     - shell: bash
       env:
         DEPLOYBOT_CONFIG: ${{ inputs.config }}

```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, prior-fix

**Notes:**

Moved ${{ github.action_path }} from the pip install run: script into env ACTION_PATH and referenced it quoted as "$ACTION_PATH" (re-applying the prior harden's patch). setup-python was already SHA-pinned.

