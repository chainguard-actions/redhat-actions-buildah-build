<!-- markdownlint-disable -->

# Hardening Report: redhat-actions--buildah-build/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **redhat-actions--buildah-build/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files reference external actions using mutable tags (e.g. @v7, @v6, @v5, @v2, @v1) instead of immutable 40-character commit SHAs. This exposes the workflows to supply-chain attacks if a tag is moved to a malicious commit. Affected references include: actions/checkout@v7, actions/setup-java@v5, docker/metadata-action@v6, redhat-actions/common/bundle-verifier@v2, redhat-actions/common/action-io-generator@v2, tcort/github-action-markdown-link-check@v1.

Locations:

- `.github/workflows/check-lowercase.yaml:28`
- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:34`
- `.github/workflows/ci.yml:40`
- `.github/workflows/ci.yml:50`
- `.github/workflows/ci.yml:56`
- `.github/workflows/containerfile_build.yml:28`
- `.github/workflows/docker_metadata_action.yml:29`
- `.github/workflows/docker_metadata_action.yml:33`
- `.github/workflows/docker_metadata_action.yml:88`
- `.github/workflows/docker_metadata_action.yml:92`
- `.github/workflows/docker_metadata_action.yml:107`
- `.github/workflows/docker_metadata_action.yml:113`
- `.github/workflows/link_check.yml:20`
- `.github/workflows/link_check.yml:21`
- `.github/workflows/multiarch.yml:28`
- `.github/workflows/multiarch.yml:87`
- `.github/workflows/multiarch.yml:145`
- `.github/workflows/multiarch.yml:155`
- `.github/workflows/multiarch.yml:161`
- `.github/workflows/scratch_build.yml:28`
- `.github/workflows/scratch_build.yml:35`
- `.github/workflows/scratch_build.yml:43`

### script-injection (severity: high)

Multiple run: blocks directly interpolate ${{ ... }} expressions inside shell command strings (sub-rule a). This causes GitHub Actions to substitute the value into the shell script before the shell parses it, enabling script injection if any value contains shell metacharacters. Affected patterns include: `buildah inspect ${{ steps.build_image.outputs.image-with-tag }}`, `podman run --rm ${{ steps.build_image_multiarch.outputs.image }}:${{ env.IMAGE_TAG }}`, `buildah images | grep '${{ env.IMAGE_NAME }}'`, `echo "Image: ${{ steps.build_image.outputs.image }}"`, and `buildah inspect ${{ steps.build_image_multiarch.outputs.image }}:${{ env.IMAGE_TAG }}`. These should be moved to env: variables and referenced as quoted shell variables (e.g. "$IMAGE_NAME") instead.

Locations:

- `.github/workflows/check-lowercase.yaml:49`
- `.github/workflows/check-lowercase.yaml:50`
- `.github/workflows/check-lowercase.yaml:51`
- `.github/workflows/check-lowercase.yaml:52`
- `.github/workflows/containerfile_build.yml:48`
- `.github/workflows/containerfile_build.yml:49`
- `.github/workflows/containerfile_build.yml:50`
- `.github/workflows/containerfile_build.yml:51`
- `.github/workflows/containerfile_build.yml:54`
- `.github/workflows/docker_metadata_action.yml:62`
- `.github/workflows/docker_metadata_action.yml:63`
- `.github/workflows/docker_metadata_action.yml:64`
- `.github/workflows/docker_metadata_action.yml:67`
- `.github/workflows/docker_metadata_action.yml:68`
- `.github/workflows/docker_metadata_action.yml:69`
- `.github/workflows/docker_metadata_action.yml:70`
- `.github/workflows/docker_metadata_action.yml:71`
- `.github/workflows/docker_metadata_action.yml:72`
- `.github/workflows/multiarch.yml:57`
- `.github/workflows/multiarch.yml:58`
- `.github/workflows/multiarch.yml:59`
- `.github/workflows/multiarch.yml:62`
- `.github/workflows/multiarch.yml:63`
- `.github/workflows/multiarch.yml:66`
- `.github/workflows/scratch_build.yml:63`
- `.github/workflows/scratch_build.yml:64`
- `.github/workflows/scratch_build.yml:65`
- `.github/workflows/scratch_build.yml:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 6 workflow files:
1. unpinned-uses: Pinned all external action references to full 40-char commit SHAs:
   - actions/checkout@v7 → @3d3c42e5aac5ba805825da76410c181273ba90b1
   - actions/setup-java@v5 → @03ad4de0992f5dab5e18fcb136590ce7c4a0ac95
   - docker/metadata-action@v6 → @dc802804100637a589fabce1cb79ff13a1411302
   - redhat-actions/common@v2 → @ad38cc90ef0aa8ba71be3efaec1ef60fdf7853b3
   - tcort/github-action-markdown-link-check@v1 → @e7c7a18363c842693fadde5d41a3bd3573a7a225
2. script-injection: Moved all ${{ steps.*.outputs.* }} and ${{ env.* }} expressions from run: shell strings into env: blocks, referencing them as plain shell variables ($BUILD_IMAGE, $BUILD_TAGS, $BUILD_IMAGE_WITH_TAG, $BUILD_DIGEST, $IMAGE_NAME, $IMAGE_TAG) in the run: scripts. Affected files: check-lowercase.yaml, containerfile_build.yml, docker_metadata_action.yml, multiarch.yml, scratch_build.yml.

