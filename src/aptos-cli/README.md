# Objective

While the Aptos CLI tool is available for different platforms and architectures,
it isn't readily available for use as a Docker image specifically for the
`linux/arm64/v8` aka `aarch64` processor architecture, more commonly known as
the M1/M2 chips that Macbooks use.

This Docker builds a simple image of the `aptos` CLI, with the CLI version corresponding directly Docker image tag:

```Dockerfile
# Uses the aptos CLI, version 4.0.0
FROM econialabs/aptos-cli:4.0.0

RUN aptos --version
# > aptos 4.0.0
```
