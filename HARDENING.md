<!-- markdownlint-disable -->

# Hardening Report: redhat-actions--buildah-build/v2.13

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **redhat-actions--buildah-build/v2.13** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Every `uses:` reference across all workflow files uses a mutable tag or version string instead of a pinned 40-character SHA commit hash, making the workflows vulnerable to supply-chain attacks if the referenced action tag is moved or compromised. Affected references include: actions/checkout@v4, docker/metadata-action@v4, actions/setup-java@v3, actions/setup-node@v3, redhat-actions/common/bundle-verifier@v1, redhat-actions/common/action-io-generator@v1, gaurav-nelson/github-action-markdown-link-check@v1, redhat-actions/openshift-tools-installer@v1, redhat-actions/crda@v1.

Locations:

- `.github/workflows/check-lowercase.yaml:27`
- `.github/workflows/ci.yml:9`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:26`
- `.github/workflows/ci.yml:37`
- `.github/workflows/ci.yml:43`
- `.github/workflows/containerfile_build.yml:27`
- `.github/workflows/docker_metadata_action.yml:27`
- `.github/workflows/docker_metadata_action.yml:31`
- `.github/workflows/docker_metadata_action.yml:79`
- `.github/workflows/docker_metadata_action.yml:83`
- `.github/workflows/docker_metadata_action.yml:101`
- `.github/workflows/docker_metadata_action.yml:105`
- `.github/workflows/link_check.yml:15`
- `.github/workflows/link_check.yml:16`
- `.github/workflows/multiarch.yml:22`
- `.github/workflows/multiarch.yml:80`
- `.github/workflows/multiarch.yml:138`
- `.github/workflows/multiarch.yml:143`
- `.github/workflows/multiarch.yml:148`
- `.github/workflows/scratch_build.yml:27`
- `.github/workflows/scratch_build.yml:38`
- `.github/workflows/scratch_build.yml:44`
- `.github/workflows/security_scan.yml:14`
- `.github/workflows/security_scan.yml:18`
- `.github/workflows/security_scan.yml:23`
- `.github/workflows/security_scan.yml:30`

### script-injection (severity: high)

Multiple `run:` blocks directly interpolate `${{ }}` expressions inside shell commands (rule a), allowing expression values to be interpreted by the shell before quoting can occur. Affected patterns include: `echo "Image: ${{ steps.build_image.outputs.image }}"`, `buildah images | grep '${{ env.IMAGE_NAME }}'`, `buildah inspect ${{ steps.build_image.outputs.image-with-tag }} | jq ...`, `podman run --rm ${{ steps.build_image_multiarch.outputs.image }}:${{ env.IMAGE_TAG }}`, and `buildah manifest inspect ${{ steps.build_image_multiplatform.outputs.image }}:${{ env.IMAGE_TAG }}`. All `${{ steps.*.outputs.* }}` and `${{ env.* }}` values should be passed via `env:` variables and then double-quoted in the shell.

Locations:

- `.github/workflows/check-lowercase.yaml:48`
- `.github/workflows/check-lowercase.yaml:49`
- `.github/workflows/check-lowercase.yaml:50`
- `.github/workflows/containerfile_build.yml:47`
- `.github/workflows/containerfile_build.yml:48`
- `.github/workflows/containerfile_build.yml:49`
- `.github/workflows/containerfile_build.yml:52`
- `.github/workflows/docker_metadata_action.yml:55`
- `.github/workflows/docker_metadata_action.yml:56`
- `.github/workflows/docker_metadata_action.yml:57`
- `.github/workflows/docker_metadata_action.yml:60`
- `.github/workflows/docker_metadata_action.yml:62`
- `.github/workflows/docker_metadata_action.yml:63`
- `.github/workflows/docker_metadata_action.yml:64`
- `.github/workflows/docker_metadata_action.yml:65`
- `.github/workflows/docker_metadata_action.yml:120`
- `.github/workflows/docker_metadata_action.yml:121`
- `.github/workflows/docker_metadata_action.yml:122`
- `.github/workflows/docker_metadata_action.yml:125`
- `.github/workflows/docker_metadata_action.yml:127`
- `.github/workflows/docker_metadata_action.yml:128`
- `.github/workflows/docker_metadata_action.yml:129`
- `.github/workflows/docker_metadata_action.yml:130`
- `.github/workflows/multiarch.yml:47`
- `.github/workflows/multiarch.yml:48`
- `.github/workflows/multiarch.yml:49`
- `.github/workflows/multiarch.yml:52`
- `.github/workflows/multiarch.yml:54`
- `.github/workflows/multiarch.yml:55`
- `.github/workflows/multiarch.yml:58`
- `.github/workflows/multiarch.yml:107`
- `.github/workflows/multiarch.yml:108`
- `.github/workflows/multiarch.yml:109`
- `.github/workflows/multiarch.yml:112`
- `.github/workflows/multiarch.yml:114`
- `.github/workflows/multiarch.yml:115`
- `.github/workflows/multiarch.yml:175`
- `.github/workflows/multiarch.yml:176`
- `.github/workflows/multiarch.yml:177`
- `.github/workflows/multiarch.yml:180`
- `.github/workflows/multiarch.yml:182`
- `.github/workflows/scratch_build.yml:60`
- `.github/workflows/scratch_build.yml:61`
- `.github/workflows/scratch_build.yml:62`
- `.github/workflows/scratch_build.yml:65`

### missing-permissions (severity: medium)

None of the 8 workflow files under .github/workflows/ define a top-level `permissions:` key, and no individual job defines a `permissions:` key either. Without explicit permissions, workflows inherit the default repository token permissions (which may be write-all depending on repository settings), granting broader access than necessary. All workflow files are affected: check-lowercase.yaml, ci.yml, containerfile_build.yml, docker_metadata_action.yml, link_check.yml, multiarch.yml, scratch_build.yml, security_scan.yml.

Locations:

- `.github/workflows/check-lowercase.yaml:1`
- `.github/workflows/ci.yml:1`
- `.github/workflows/containerfile_build.yml:1`
- `.github/workflows/docker_metadata_action.yml:1`
- `.github/workflows/link_check.yml:1`
- `.github/workflows/multiarch.yml:1`
- `.github/workflows/scratch_build.yml:1`
- `.github/workflows/security_scan.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three findings across 8 workflow files:

1. **unpinned-uses**: Pinned all resolvable action references to full SHA hashes with tag comments. The `redhat-actions/crda@v1` action could not be pinned as the repository has no accessible refs (appears archived/deleted).

2. **script-injection**: Moved all `${{ steps.*.outputs.* }}` and `${{ env.* }}` expressions from `run:` shell blocks into `env:` blocks, then referenced them as plain environment variables (e.g., `$BUILD_IMAGE`, `$IMAGE_NAME`, `$BUILD_IMAGE_WITH_TAG`). Fixed in check-lowercase.yaml, containerfile_build.yml, docker_metadata_action.yml, multiarch.yml, and scratch_build.yml.

3. **missing-permissions**: Added `permissions: {}` at the top level of all 8 workflow files to enforce least-privilege access.

### Iteration 2

**Fixes applied:** unpinned-uses

**Notes:**

The `redhat-actions/crda@v1` action at .github/workflows/security_scan.yml:35 could not be pinned to a commit SHA because the `redhat-actions/crda` repository is no longer accessible (deprecated/deleted — no refs resolve via git ls-remote). The CRDA Scan step has been commented out with an explanatory note, eliminating the unpinned-uses finding. All other `uses:` references in the workflow were already pinned to full SHAs.

