# syntax=docker/dockerfile:1
ARG DEBIAN_FRONTEND=noninteractive
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

#Define runtime args.
ARG BASE_IMAGE=ubuntu:24.04
ARG ROS_DISTRO=jazzy

#First build on top of Ubuntu - get the "computer ready and then build ROS2 img"
FROM ${BASE_IMAGE} as base
# setup timezone
RUN echo 'Etc/UTC' > /etc/timezone && \
    ln -s /usr/share/zoneinfo/Etc/UTC /etc/localtime && \
    apt-get update && \
    apt-get install -q -y --no-install-recommends tzdata && \
    rm -rf /var/lib/apt/lists/*

#install general terminal (CI/CD) tools
RUN echo 'CI/CD pckgs' && \
    apt-get update && \
    apt-get install -q -y --no-install-recommends && \
    build-essential && \
    cmake && \
    git && \
    git-lfs && \
    openssh-client && \
    curl && \
    ca-certificates && \
    gnupg && \
    locales && \
    python3-pip && \
    python3-venv && \
    tmux && \
    htop && \
    vim && \
    less && \
    iproute2 && \
    iputils-ping && \
    bash-completion && \
    sudo && \
    rm -rf /var/lib/apt/lists/*
