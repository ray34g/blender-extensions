# syntax=docker/dockerfile:1.4
################################################################################
# Stage 0: download & unpack Blender into /opt/blender (builder stage)
################################################################################
FROM ubuntu:22.04 AS blender-fetch

ARG BLENDER_RELEASE=Blender4.2
ARG BLENDER_VERSION=4.2.10
ARG BLENDER_TAR=blender-${BLENDER_VERSION}-linux-x64.tar.xz
ARG BLENDER_URL=https://download.blender.org/release/${BLENDER_RELEASE}/${BLENDER_TAR}

RUN apt-get update && \
    apt-get install -y --no-install-recommends curl ca-certificates xz-utils && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /tmp
RUN curl -fsSL "${BLENDER_URL}" -o "${BLENDER_TAR}" && \
    tar -xJf "${BLENDER_TAR}" && \
    mv blender-${BLENDER_VERSION}-linux-x64 /opt/blender

################################################################################
# Stage 1: final runner image  ────────────────────────────────────────────────
################################################################################
FROM ghcr.io/actions/actions-runner:latest

USER root

LABEL maintainer="ray34g <Go.Ray@ray34g.com"

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      libgl1                \
      libglu1-mesa          \
      libxcb1               \
      libx11-6              \
      libxext6              \
      libxi6                \
      libxrender1           \
      libxrandr2            \
      libxxf86vm1           \         
      libxkbcommon0         \
      libsm6                \         
      libdbus-1-3           \
      libtbb2               \
      python3               \
      xz-utils              \
      ca-certificates       \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

COPY --from=blender-fetch /opt/blender /opt/blender

ENV PATH="/opt/blender:${PATH}"

RUN blender --version

# actions-runner's ENTRYPOINT/CMD