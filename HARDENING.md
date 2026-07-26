<!-- markdownlint-disable -->

# Hardening Report: redhat-actions--buildah-build/v3.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **redhat-actions--buildah-build/v3.0.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files reference external actions using mutable version tags (e.g., @v7, @v6, @v5, @v2, @v1) instead of immutable full 40-character SHA commit hashes. This exposes the workflows to supply-chain attacks where a tag could be silently moved to point to malicious code. Affected references include: actions/checkout@v7, actions/setup-java@v5, docker/metadata-action@v6, redhat-actions/common/bundle-verifier@v2, redhat-actions/common/action-io-generator@v2, tcort/github-action-markdown-link-check@v1.

Locations:

- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:46`
- `.github/workflows/ci.yml:57`
- `.github/workflows/containerfile_build.yml:25`
- `.github/workflows/docker_metadata_action.yml:30`
- `.github/workflows/docker_metadata_action.yml:33`
- `.github/workflows/docker_metadata_action.yml:45`
- `.github/workflows/link_check.yml:20`
- `.github/workflows/link_check.yml:21`
- `.github/workflows/multiarch.yml:32`
- `.github/workflows/multiarch.yml:49`
- `.github/workflows/scratch_build.yml:31`
- `.github/workflows/scratch_build.yml:43`

### script-injection (severity: high)

Multiple run: blocks across workflow files directly interpolate ${{ }} expressions inside shell command strings (rule a). GitHub Actions performs template substitution before the shell processes the string, so any special characters in the substituted value are interpreted by the shell. Affected patterns include: `echo "Image: ${{ steps.build_image.outputs.image }}"`, `buildah images | grep '${{ env.IMAGE_NAME }}'`, `buildah inspect ${{ steps.build_image.outputs.image-with-tag }}`, `buildah inspect ${{ steps.build_image_multiarch.outputs.image }}:${{ env.IMAGE_TAG }}`, and `podman run --rm ${{ steps.build_image_multiarch.outputs.image }}:${{ env.IMAGE_TAG }}`. These values (steps.*.outputs.* and env.*) are workflow-controllable and must be passed via env: variables and then double-quoted in the shell script.

Locations:

- `.github/workflows/containerfile_build.yml:48`
- `.github/workflows/containerfile_build.yml:55`
- `.github/workflows/containerfile_build.yml:85`
- `.github/workflows/containerfile_build.yml:92`
- `.github/workflows/docker_metadata_action.yml:73`
- `.github/workflows/docker_metadata_action.yml:80`
- `.github/workflows/docker_metadata_action.yml:83`
- `.github/workflows/multiarch.yml:65`
- `.github/workflows/multiarch.yml:72`
- `.github/workflows/multiarch.yml:75`
- `.github/workflows/multiarch.yml:81`
- `.github/workflows/scratch_build.yml:71`
- `.github/workflows/scratch_build.yml:78`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all unpinned action references by resolving full 40-character SHA commit hashes for: actions/checkout@v7 (3d3c42e5aac5ba805825da76410c181273ba90b1), actions/setup-java@v5 (03ad4de0992f5dab5e18fcb136590ce7c4a0ac95), docker/metadata-action@v6 (dc802804100637a589fabce1cb79ff13a1411302), redhat-actions/common@v2 (ad38cc90ef0aa8ba71be3efaec1ef60fdf7853b3), and tcort/github-action-markdown-link-check@v1 (e7c7a18363c842693fadde5d41a3bd3573a7a225). Fixed all script injection vulnerabilities in containerfile_build.yml, docker_metadata_action.yml, multiarch.yml, and scratch_build.yml by moving ${{ steps.*.outputs.* }} and ${{ env.* }} expressions out of run: shell strings into step-level env: blocks, then referencing them as plain double-quoted environment variables in the shell scripts.

