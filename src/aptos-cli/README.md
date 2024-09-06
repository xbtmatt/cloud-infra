<!--
cspell:word aarch
cspell:word toplevel
cspell:word Macbooks
-->

# Objective

The Aptos CLI is available for various platforms and architectures as a
standalone executable; however, there isn't a ready-to-use Docker image for the
`aarch64` processor architecture (labeled as `linux/arm64/v8` in Docker).

This architecture is particularly significant, as it's used in the arm-based
Apple silicon found in newer Macbooks.

The image built from this Dockerfile serves to address this gap and provide a
solution for users working with these systems.

It builds an image of the `aptos` CLI for `linux/arm64` and `linux/amd64`, with
the CLI version corresponding directly Docker image tag:

```Dockerfile
# Uses the aptos CLI, version 4.0.0
FROM xbtmatt/aptos-cli:4.0.0

RUN aptos --version
# > aptos 4.0.0
```

## Building the image and pushing it to the `xbtmatt` Docker hub

To build a Docker image with a specific version of the Aptos CLI, simply push
the corresponding version tag to GitHub to trigger the GitHub workflow that
builds the image in CI:

```shell
git tag aptos-cli-v4.0.0
```

This will trigger the GitHub `push-aptos-cli.yaml` workflow to build the `aptos`
CLI Docker image and subsequently push it to the `xbtmatt` Dockerhub
repository as `xbtmatt/aptos-cli:4.0.0`.

## Triggering the workflow manually

The action is also set to trigger manually on `workflow_dispatch`.

You will be prompted to input the version, which will work with both of the
following formats:

`cli_version: v4.0.0`

or

`cli_version: 4.0.0`

Since the Dockerfile strips the `v` when parsing the `ARG CLI_VERSION` value.

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
