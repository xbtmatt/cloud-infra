# Objective

While the Aptos CLI tool is available for different platforms and architectures,
it isn't readily available for use as a Docker image specifically for the
`linux/arm64/v8` aka `aarch64` processor architecture, more commonly known as
the M1/M2 chips that Macbooks use.

This Docker builds a simple image of the `aptos` CLI, with the CLI version
corresponding directly Docker image tag:

```Dockerfile
# Uses the aptos CLI, version 4.0.0
FROM econialabs/aptos-cli:4.0.0

RUN aptos --version
# > aptos 4.0.0
```

## Building the image and pushing it to the `econialabs` Docker hub

To build a Docker image with a specific version of the Aptos CLI, simply push
the corresponding version tag to GitHub to trigger the GitHub workflow that
builds the image in CI:

```shell
git tag aptos-cli-v4.0.0
```

This will trigger the GitHub `push-aptos-cli.yaml` workflow to build the `aptos`
CLI Docker image and subsequently push it to the `econialabs` Dockerhub
repository as `econialabs/aptos-cli:4.0.0`.

## Multi-architecture support

Currently the GitHub action triggers builds for `arm64` and `amd64`.

## Building it yourself locally

If you'd like to build the image yourself, you can simply pass the CLI version
as a `build-arg`.

A simple `bash` script for this process might be something like:

```bash
#!/bin/bash

# From the root of this repository.
git_root=$(git rev-parse --show-toplevel)

username=YOUR_DOCKERHUB_USERNAME
version=v4.0.0

docker buildx build \
  --platform linux/amd64,linux/arm64 \
  --build-arg GIT_TAG=aptos-cli-$version \
  -t $username/aptos-cli:$version \
  -f $git_root/src/aptos-cli/Dockerfile \
  --push \
  .
```
