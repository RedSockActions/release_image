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

###### Made by RedSock with love for coding 