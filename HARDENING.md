<!-- markdownlint-disable -->

# Hardening Report: medyagh--setup-minikube/v0.0.21

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **medyagh--setup-minikube/v0.0.21** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions by mutable tags or branch names instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks:
- `.github/workflows/auto-tag-latest.yml`: `Actions-R-Us/actions-tagger@v2` (tag)
- `.github/workflows/dry-run.yml`: `medyagh/info-block@main` (branch)
- `.github/workflows/macos-test.yml`: `medyagh/info-block@main` (branch)
- `.github/workflows/test.yml`: `medyagh/info-block@main` (branch)

Locations:

- `.github/workflows/auto-tag-latest.yml:13`
- `.github/workflows/dry-run.yml:33`
- `.github/workflows/macos-test.yml:43`
- `.github/workflows/test.yml:55`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` key and no job-level `permissions:` keys on any job. Without explicit permissions, workflows inherit the repository's default token permissions (often `write-all`), granting unnecessary access:
- `build.yml`
- `dry-run.yml`
- `macos-test.yml`
- `test.yml`

Locations:

- `.github/workflows/build.yml:1`
- `.github/workflows/dry-run.yml:1`
- `.github/workflows/macos-test.yml:1`
- `.github/workflows/test.yml:1`

### unsafe-shell (severity: high)

Two workflow files pipe remote content directly to a shell interpreter without first downloading and inspecting the script. The pattern `curl -fsSL https://github.com/minikube-machine/vmnet-helper/releases/latest/download/install.sh | sudo VMNET_INTERACTIVE=0 bash` fetches and executes a remote shell script in a single pipeline. If the remote URL is compromised or redirected, arbitrary code runs with `sudo` privileges on the runner. The script should be downloaded to a file first, its integrity verified (e.g., checksum), and then executed separately.

Locations:

- `.github/workflows/dry-run.yml:44`
- `.github/workflows/macos-test.yml:50`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, unsafe-shell

**Notes:**

Fixed all three findings across 5 workflow files:

1. unpinned-uses: Pinned Actions-R-Us/actions-tagger@v2 to SHA 330ddfac760021349fef7ff62b372f2f691c20fb in auto-tag-latest.yml, and medyagh/info-block@main to SHA 16d412ee23c6861a696c0d140899f7aa151b57cc in dry-run.yml, macos-test.yml, and test.yml.

2. missing-permissions: Added `permissions: {}` top-level block to build.yml, dry-run.yml, macos-test.yml, and test.yml.

3. unsafe-shell: In both dry-run.yml and macos-test.yml, replaced `curl ... | sudo VMNET_INTERACTIVE=0 bash` with a two-step approach: download to /tmp/vmnet-install.sh first, then execute separately with `sudo VMNET_INTERACTIVE=0 bash /tmp/vmnet-install.sh`.

