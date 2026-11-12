# syntax=docker/dockerfile:1
ARG BASE_IMAGE=ubuntu:24.04

FROM ${BASE_IMAGE} AS base
ARG DEBIAN_FRONTEND=noninteractive
ARG ROS_DISTRO=jazzy
ARG ROS_APT_SOURCE_VERSION=1.3.0
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8

#install general terminal (CI/CD) tools
RUN echo 'CI/CD pckgs' \
    && apt-get update \
    && apt-get install -q -y --no-install-recommends \
    build-essential \
    cmake \
    git \
    git-lfs \
    openssh-client \
    curl \
    ca-certificates \
    gnupg2 \
    python3-pip \
    python3-venv \
    python3-argcomplete \
    tmux \
    htop \
    vim \
    less \
    iproute2 \
    iputils-ping \
    bash-completion \
    sudo \
    && rm -rf /var/lib/apt/lists/*

#ROS env setup --> follows .deb pckg setup from https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html

RUN echo 'ENABLE REQUIRED ROS REPOS' \
    && apt-get update \
    && apt-get install -q -y --no-install-recommends software-properties-common \
    && add-apt-repository universe \
    && rm -rf /var/lib/apt/lists/*

RUN . /etc/os-release \
 && curl -fsSL -o /tmp/ros2-apt-source.deb \
      "https://github.com/ros-infrastructure/ros-apt-source/releases/download/${ROS_APT_SOURCE_VERSION}/ros2-apt-source_${ROS_APT_SOURCE_VERSION}.${UBUNTU_CODENAME}_all.deb" \
 && dpkg -i /tmp/ros2-apt-source.deb \
 && rm /tmp/ros2-apt-source.deb

# ROS docs: ros-dev-tools needs noble-updates + noble-backports (already present in ubuntu:24.04)
RUN f=/etc/apt/sources.list.d/ubuntu.sources \
 && if ! grep -q '^Suites:.*noble-updates.*noble-backports' "$f"; then \
      sed -i '0,/^Suites:.*/s//Suites: noble noble-updates noble-backports/' "$f"; \
    fi

#Install bare bones ROS2 and add pckgs based on role
RUN apt-get update \
 && apt-get full-upgrade -y \
 && apt-get install -y --no-install-recommends \ 
    ros-dev-tools \
    ros-${ROS_DISTRO}-ros-base \
 && rm -rf /var/lib/apt/lists/*

RUN rosdep init

