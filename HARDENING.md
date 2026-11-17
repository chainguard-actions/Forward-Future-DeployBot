<!-- markdownlint-disable -->

# Hardening Report: Forward-Future--DeployBot/v0.2.11

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **Forward-Future--DeployBot/v0.2.11** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): the composite action's run block puts a GitHub Actions expression straight into the shell command: `run: python -m pip install "${{ github.action_path }}"`. The value is substituted into the script text before the shell parses it. Fix: use the runner-provided env var instead, e.g. `python -m pip install "$GITHUB_ACTION_PATH"`.

Locations:

- `action.yml:27`

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 9574a17..331932c 100644
--- a/action.yml
+++ b/action.yml
@@ -20,11 +20,30 @@ inputs:
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

In action.yml, the pip install step had `${{ github.action_path }}` written directly into its run: script. I replaced it with the runner-provided `$GITHUB_ACTION_PATH`, double-quoted, so no expression is substituted into the script text any more. The previous harden fixed the same line by mapping the path through an env: entry; this does the same job without adding a variable. The setup-python action was already pinned to a full SHA, and the other step already passes its inputs through env:, so nothing else needed changing. Nothing was run to test the edit.

