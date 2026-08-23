<!-- markdownlint-disable -->

# Hardening Report: redhat-actions--buildah-build/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **redhat-actions--buildah-build/v3.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files use mutable tag-based refs instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks if the referenced tag is moved. Failing references include: actions/checkout@v7 (all files), docker/metadata-action@v6, actions/setup-java@v5, redhat-actions/common/bundle-verifier@v2, redhat-actions/common/action-io-generator@v2, tcort/github-action-markdown-link-check@v1.

Locations:

- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:28`
- `.github/workflows/ci.yml:36`
- `.github/workflows/ci.yml:40`
- `.github/workflows/ci.yml:49`
- `.github/workflows/ci.yml:55`
- `.github/workflows/containerfile_build.yml:26`
- `.github/workflows/containerfile_build.yml:64`
- `.github/workflows/containerfile_build.yml:97`
- `.github/workflows/dependabot-rebuild.yml:14`
- `.github/workflows/docker_metadata_action.yml:27`
- `.github/workflows/docker_metadata_action.yml:31`
- `.github/workflows/docker_metadata_action.yml:100`
- `.github/workflows/docker_metadata_action.yml:104`
- `.github/workflows/docker_metadata_action.yml:117`
- `.github/workflows/docker_metadata_action.yml:122`
- `.github/workflows/link_check.yml:20`
- `.github/workflows/link_check.yml:21`
- `.github/workflows/multiarch.yml:30`
- `.github/workflows/multiarch.yml:89`
- `.github/workflows/multiarch.yml:143`
- `.github/workflows/multiarch.yml:153`
- `.github/workflows/multiarch.yml:159`
- `.github/workflows/scratch_build.yml:30`
- `.github/workflows/scratch_build.yml:37`
- `.github/workflows/scratch_build.yml:44`

### script-injection (severity: high)

Multiple run: blocks directly interpolate ${{ }} expressions into shell commands (sub-rule a), allowing template substitution before the shell parses the string. Affected patterns include: `echo "Image: ${{ steps.build_image.outputs.image }}"`, `buildah images | grep '${{ env.IMAGE_NAME }}'`, `buildah inspect ${{ steps.build_image.outputs.image-with-tag }} | jq ...`, and `podman run --rm ${{ steps.build_image_multiarch.outputs.image }}:${{ env.IMAGE_TAG }}`. The steps.*.outputs.* values are action outputs that flow through YAML template substitution before the shell ever sees them.

Locations:

- `.github/workflows/containerfile_build.yml:44`
- `.github/workflows/containerfile_build.yml:45`
- `.github/workflows/containerfile_build.yml:46`
- `.github/workflows/containerfile_build.yml:47`
- `.github/workflows/containerfile_build.yml:50`
- `.github/workflows/containerfile_build.yml:77`
- `.github/workflows/containerfile_build.yml:78`
- `.github/workflows/containerfile_build.yml:79`
- `.github/workflows/containerfile_build.yml:80`
- `.github/workflows/containerfile_build.yml:83`
- `.github/workflows/docker_metadata_action.yml:60`
- `.github/workflows/docker_metadata_action.yml:61`
- `.github/workflows/docker_metadata_action.yml:62`
- `.github/workflows/docker_metadata_action.yml:65`
- `.github/workflows/docker_metadata_action.yml:68`
- `.github/workflows/docker_metadata_action.yml:69`
- `.github/workflows/docker_metadata_action.yml:70`
- `.github/workflows/docker_metadata_action.yml:71`
- `.github/workflows/docker_metadata_action.yml:72`
- `.github/workflows/docker_metadata_action.yml:73`
- `.github/workflows/multiarch.yml:55`
- `.github/workflows/multiarch.yml:56`
- `.github/workflows/multiarch.yml:57`
- `.github/workflows/multiarch.yml:60`
- `.github/workflows/multiarch.yml:63`
- `.github/workflows/multiarch.yml:64`
- `.github/workflows/multiarch.yml:65`
- `.github/workflows/multiarch.yml:100`
- `.github/workflows/scratch_build.yml:68`
- `.github/workflows/scratch_build.yml:69`
- `.github/workflows/scratch_build.yml:70`
- `.github/workflows/scratch_build.yml:73`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all workflow files in hardened/action/.github/workflows/:

1. **unpinned-uses**: Pinned all action references to full SHA commits:
   - actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1
   - docker/metadata-action@v6 → @dc802804100637a589fabce1cb79ff13a1411302
   - actions/setup-java@v5 → @b6effb05e454b25005698d916606bdc6ffcbf961
   - redhat-actions/common/bundle-verifier@v2 → @19c680ff95a52ee905481b54fc08d5c47788600c
   - redhat-actions/common/action-io-generator@v2 → @19c680ff95a52ee905481b54fc08d5c47788600c
   - tcort/github-action-markdown-link-check@v1 → @e047c5b37f24ab722bbef1a27b6fab7f96bc4068
   Applied to: ci.yml, containerfile_build.yml, dependabot-rebuild.yml, docker_metadata_action.yml, link_check.yml, multiarch.yml, scratch_build.yml

2. **script-injection**: Moved all ${{ steps.*.outputs.* }} expressions from run: shell blocks into step env: blocks, referencing them as plain environment variables ($BUILD_IMAGE, $BUILD_TAGS, $BUILD_IMAGE_WITH_TAG, $BUILD_DIGEST, $BUILD_IMAGE_WITH_TAG). Also fixed buildah images | grep to use the env var $IMAGE_NAME instead of ${{ env.IMAGE_NAME }} template expression. Applied to: containerfile_build.yml, docker_metadata_action.yml, multiarch.yml, scratch_build.yml.

