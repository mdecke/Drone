# syntax=docker/dockerfile:1
# Build from the repo root:  docker build -f docker/Dockerfile --target dev .
ARG ROS_DISTRO=jazzy

# --- manifests ---------------------------------------------------------------
# Collect only package.xml files. The rosdep layer below depends on this
# stage's output, so it is rebuilt only when a package.xml changes, not on
# every source edit.
FROM ros:${ROS_DISTRO}-ros-base-noble AS manifests
COPY . /src
RUN mkdir /manifests \
 && cd /src \
 && find . -name package.xml -exec cp --parents {} /manifests \;

# --- base: ROS + every dependency declared in the repo (used by CI) ----------
FROM ros:${ROS_DISTRO}-ros-base-noble AS base
ARG DEBIAN_FRONTEND=noninteractive
SHELL ["/bin/bash", "-c"]

COPY --from=manifests /manifests /tmp/manifests
RUN apt-get update \
 && rosdep update --rosdistro "$ROS_DISTRO" \
 && if [ -n "$(find /tmp/manifests -name package.xml)" ]; then \
      rosdep install --from-paths /tmp/manifests --ignore-src -y --rosdistro "$ROS_DISTRO"; \
    fi \
 && rm -rf /var/lib/apt/lists/* /tmp/manifests

WORKDIR /ws

# --- dev: base + simulation, GUI and debug tools, non-root user --------------
FROM base AS dev
ARG DEBIAN_FRONTEND=noninteractive
ARG USER_UID=1000
ARG USER_GID=1000

RUN apt-get update && apt-get install -y --no-install-recommends \
      ros-${ROS_DISTRO}-ros-gz \
      ros-${ROS_DISTRO}-rviz2 \
      ros-${ROS_DISTRO}-rqt-common-plugins \
      ros-${ROS_DISTRO}-plotjuggler-ros \
      gdb \
      valgrind \
      clangd \
      clang-format \
      clang-tidy \
      htop \
      tmux \
      vim \
      less \
      curl \
      iputils-ping \
      iproute2 \
      sudo \
      bash-completion \
 && rm -rf /var/lib/apt/lists/*

# Ubuntu 24.04 images ship a user "ubuntu" with UID/GID 1000; reuse it and
# remap only if the host user differs (bind-mounted files stay yours).
RUN if [ "$USER_GID" != "1000" ]; then groupmod -g "$USER_GID" ubuntu; fi \
 && if [ "$USER_UID" != "1000" ]; then usermod -u "$USER_UID" ubuntu; fi \
 && echo 'ubuntu ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/ubuntu \
 && chmod 0440 /etc/sudoers.d/ubuntu

USER ubuntu
RUN rosdep update --rosdistro "$ROS_DISTRO" \
 && echo "source /opt/ros/$ROS_DISTRO/setup.bash" >> ~/.bashrc

WORKDIR /ws
CMD ["bash"]
