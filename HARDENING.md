<!-- markdownlint-disable -->

# Hardening Report: Forward-Future--DeployBot/v0.2.14

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Forward-Future--DeployBot/v0.2.14** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): a GitHub Actions expression is placed directly inside a run: script in a composite step: `run: python -m pip install "${{ github.action_path }}"`. The value is substituted into the script before bash parses it. Fix: move it into an env var (e.g. env: ACTION_PATH: ${{ github.action_path }}) and use `python -m pip install "$ACTION_PATH"`, or use the runner-provided "$GITHUB_ACTION_PATH".

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

In action.yml, I moved the github.action_path expression out of the pip install run script and into an ACTION_PATH variable in the step's env: block. The script now reads it as "$ACTION_PATH". This puts back the same change the previous harden made. No other changes were needed: the uses: line is already pinned to a commit SHA, and the deploybot step already passes its inputs through env:. I haven't run the action or its tests after the edit.

