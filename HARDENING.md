<!-- markdownlint-disable -->

# Hardening Report: redhat-actions--buildah-build/v3.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **redhat-actions--buildah-build/v3.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All uses: references across every workflow file use mutable version tags instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the upstream tag is moved. Failing references include: actions/checkout@v7, redhat-actions/common/bundle-verifier@v2, redhat-actions/common/action-io-generator@v2 (ci.yml); actions/checkout@v7 (containerfile_build.yml); actions/checkout@v7, docker/metadata-action@v6, actions/setup-java@v5 (docker_metadata_action.yml); actions/checkout@v7 (check-lowercase.yaml); actions/checkout@v7, tcort/github-action-markdown-link-check@v1 (link_check.yml); actions/checkout@v7, actions/setup-java@v5 (multiarch.yml); actions/checkout@v7, actions/setup-java@v5 (scratch_build.yml).

Locations:

- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:32`
- `.github/workflows/ci.yml:42`
- `.github/workflows/ci.yml:48`
- `.github/workflows/ci.yml:57`
- `.github/workflows/containerfile_build.yml:30`
- `.github/workflows/docker_metadata_action.yml:27`
- `.github/workflows/docker_metadata_action.yml:33`
- `.github/workflows/docker_metadata_action.yml:62`
- `.github/workflows/docker_metadata_action.yml:80`
- `.github/workflows/docker_metadata_action.yml:86`
- `.github/workflows/check-lowercase.yaml:27`
- `.github/workflows/link_check.yml:22`
- `.github/workflows/link_check.yml:23`
- `.github/workflows/multiarch.yml:27`
- `.github/workflows/multiarch.yml:72`
- `.github/workflows/multiarch.yml:78`
- `.github/workflows/scratch_build.yml:27`
- `.github/workflows/scratch_build.yml:35`
- `.github/workflows/scratch_build.yml:41`

### script-injection (severity: high)

Multiple run: blocks directly interpolate ${{ ... }} expressions into shell commands (sub-rule a), allowing an attacker to inject arbitrary shell code. Affected patterns include: `buildah images | grep '${{ env.IMAGE_NAME }}'`, `buildah inspect ${{ steps.build_image.outputs.image-with-tag }} | jq ...`, `buildah manifest inspect ${{ steps.build_image_multiplatform.outputs.image }}:${{ env.IMAGE_TAG }}`, `podman run --rm ${{ steps.build_image_multiarch.outputs.image }}:${{ env.IMAGE_TAG }}`, and `echo "Image: ${{ steps.build_image.outputs.image }}"`. Even env.* and steps.*.outputs.* values flow through YAML template substitution before the shell sees them, making them injection vectors.

Locations:

- `.github/workflows/containerfile_build.yml:50`
- `.github/workflows/containerfile_build.yml:51`
- `.github/workflows/containerfile_build.yml:52`
- `.github/workflows/containerfile_build.yml:53`
- `.github/workflows/containerfile_build.yml:57`
- `.github/workflows/docker_metadata_action.yml:57`
- `.github/workflows/docker_metadata_action.yml:58`
- `.github/workflows/docker_metadata_action.yml:59`
- `.github/workflows/docker_metadata_action.yml:62`
- `.github/workflows/docker_metadata_action.yml:65`
- `.github/workflows/docker_metadata_action.yml:66`
- `.github/workflows/docker_metadata_action.yml:67`
- `.github/workflows/docker_metadata_action.yml:68`
- `.github/workflows/docker_metadata_action.yml:69`
- `.github/workflows/docker_metadata_action.yml:70`
- `.github/workflows/check-lowercase.yaml:52`
- `.github/workflows/check-lowercase.yaml:53`
- `.github/workflows/check-lowercase.yaml:54`
- `.github/workflows/check-lowercase.yaml:55`
- `.github/workflows/multiarch.yml:55`
- `.github/workflows/multiarch.yml:56`
- `.github/workflows/multiarch.yml:57`
- `.github/workflows/multiarch.yml:60`
- `.github/workflows/multiarch.yml:63`
- `.github/workflows/multiarch.yml:64`
- `.github/workflows/multiarch.yml:67`
- `.github/workflows/scratch_build.yml:57`
- `.github/workflows/scratch_build.yml:58`
- `.github/workflows/scratch_build.yml:59`
- `.github/workflows/scratch_build.yml:62`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all unpinned action references across 6 workflow files (ci.yml, containerfile_build.yml, docker_metadata_action.yml, check-lowercase.yaml, link_check.yml, multiarch.yml, scratch_build.yml) by pinning to full 40-character SHA commits with tag comments. Fixed all script injection vulnerabilities by moving ${{ }} expressions from run: shell blocks into step env: blocks, then referencing them as plain environment variables ($VAR_NAME). Affected steps included Echo Outputs, Check images created, Check image metadata, Run image, and Check manifest steps across containerfile_build.yml, docker_metadata_action.yml, check-lowercase.yaml, multiarch.yml, and scratch_build.yml.

