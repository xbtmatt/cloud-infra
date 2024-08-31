# cspell:word RUSTFLAGS
# cspell:word libudev
# cspell:word libclang
# cspell:word libpq
# cspell:word libdw

ARG GIT_REPO=https://github.com/aptos-labs/aptos-core.git
ARG CLI_VERSION

# Remove the leading `v` if the version begins with it.
ARG GIT_TAG="aptos-cli-v$(echo $CLI_VERSION | sed 's/^v//')"

ARG CLI_BINARY=/aptos-core/target/cli/aptos

FROM rust:1-bookworm AS aptos-cli

ARG GIT_REPO
ARG GIT_TAG
ARG CLI_BINARY

ENV CARGO_NET_GIT_FETCH_WITH_CLI=true

RUN git clone --depth 1 --branch $GIT_TAG $GIT_REPO \
    && apt-get update && apt-get install --no-install-recommends -y \
        libudev-dev=252.26-1~deb12u2 \
        build-essential=12.9 \
        libclang-dev=1:14.0-55.7~deb12u1 \
        libpq-dev=15.8-0+deb12u1 \
        libssl-dev=3.0.13-1~deb12u1 \
        libdw-dev=0.188-2.1 \
        pkg-config=1.8.1-1 \
        lld=1:14.0-55.7~deb12u1 \
        curl=7.88.1-10+deb12u6 \
    && rm -rf /var/lib/apt/lists/*

# Resolve outdated lockfile from upstream tag, build the binary,
# and strip it to reduce its size.
RUN cargo update --manifest-path /aptos-core/Cargo.toml && \
    RUSTFLAGS="--cfg tokio_unstable" cargo build \
    --bin aptos \
    --manifest-path /aptos-core/Cargo.toml \
    --profile cli && \
    strip -s $CLI_BINARY

FROM ubuntu:noble
WORKDIR /app

# Copy the executable to `/usr/local/bin` and add it to the PATH.
ENV PATH=/usr/local/bin:$PATH
ARG CLI_BINARY
COPY --from=aptos-cli $CLI_BINARY /usr/local/bin

STOPSIGNAL SIGKILL
