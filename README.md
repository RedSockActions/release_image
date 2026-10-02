# Release Image Action v1

Build and releases image to given repository

## Brief info

This action builds and pushes image to docker registry

## Example

```yaml
name: master-actions
run-name: RELEASE
on:
  push:
    tags:
      - '*'

jobs:

  registry_release:
    runs-on: ubuntu-latest
    steps:
      - name: Release
        if: ${{ needs.tag-release.outputs.tag }} != ""
        uses: RedSockActions/release_image@v1
        with:
          REGISTRY_USER: ${{ secrets.REGISTRY_USER }}
          REGISTRY_PWD:  ${{ secrets.REGISTRY_PWD }}
```
### **THIS PARTICULAR EXAMPLE WON'T WORK IF YOU RELEASED TAG VIA ANOTHER ACTION**
#### Solution
1. You might consider to use non-standard github token when tag is generated
2. Join tag release and image release in one workflow

## Inputs 

### REGISTRY_URL
Optional value

Defines url for Docker registry to push to 

Default value will push to public Dockerhub repository 

### REGISTRY_USER
Obligatory value

Defines username for user who pushes image

### REGISTRY_PWD
Obligatory value

Defines password (token) for user who pushes image

### DOCKERFILE
Optional value

Path to the Dockerfile to build. Defaults to `Dockerfile`. Use this to build an
alternate image (e.g. `Dockerfile.omnibus`) from the same repo in a separate job.

### TAG_LATEST
Optional value

Defaults to `true`. Set to `false` to push only the `:$VERSION` tag and skip
`:latest` — useful when `latest` should be promoted separately (e.g. behind a
manual approval), see `RedSockActions/promote_tag`.

### TAG_SUFFIX
Optional value

Appended to both the version tag and the latest tag (e.g. `-omnibus`), so a
variant image built from a different Dockerfile can be stored as
`$IMAGE_NAME:$VERSION-omnibus` / `$IMAGE_NAME:latest-omnibus` in the same
repository instead of needing a separate `IMAGE_NAME`.

### BUILD_ARGS
Optional value

One `KEY=VALUE` per line, each passed to `docker buildx build` as
`--build-arg` — e.g. `VERSION=v1.2.3` to bake a release tag into the binary
via `-ldflags`. Ignored when `MODE` is `promote`.

### MODE
Optional value

Defaults to `build`. Set to `promote` to skip the build entirely and instead
copy the already-pushed `:$VERSION$TAG_SUFFIX` manifest to
`:latest$TAG_SUFFIX` via `docker buildx imagetools create` — no checkout, no
rebuild, works for multi-arch manifests as-is. Useful behind a manual
approval gate (e.g. a GitHub Environment with required reviewers) so
`:latest` only moves once a human confirms the release:

```yaml
promote-latest:
  needs: [docker-registry-release]
  environment: promote-latest
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
      with:
        ref: ${{ github.ref }}
        fetch-depth: 0

    - uses: RedSockActions/release_image@v1
      with:
        MODE: promote
        DISABLE_CHECKOUT: true
        REGISTRY_USER: redsockruf
        REGISTRY_PWD: ${{ secrets.REGISTRY_PWD }}
        IMAGE_NAME: ${{ vars.IMAGE_NAME }}
```

###### Made by RedSock with love for coding 