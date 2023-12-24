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

###### Made by RedSock with love for coding 