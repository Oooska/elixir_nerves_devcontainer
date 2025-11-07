FROM elixir:1.18

LABEL org.opencontainers.image.description="Elixir devcontainer for VSCode or Codespaces"
LABEL org.opencontainers.image.source=https://github.com/hspaans/elixir-devcontainer

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# This Dockerfile adds a non-root user with sudo access. Use the "remoteUser"
# property in devcontainer.json to use it. On Linux, the container user's GID/UIDs
# will be updated to match your local UID/GID (when using the dockerFile property).
# See https://aka.ms/vscode-remote/containers/non-root-user for details.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Configure apt and install packages
# hadolint ignore=DL3008
RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils dialog 2>&1 \
    #
    # Install bash tree
    && apt-get -y install --no-install-recommends tree\
    # Install git iproute2, procps, lsb-release (useful for CLI installs)
    && apt-get -y install --no-install-recommends git iproute2 procps iproute2 lsb-release ca-certificates \
    #
    && apt-get -y install inotify-tools \
    # Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
    && groupadd --gid $USER_GID $USERNAME \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    # [Optional] Add sudo support for the non-root user
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME\
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    #
    # Install libraries for Nerves development
    && apt-get -y install build-essential automake autoconf git libmnl-dev squashfs-tools ssh-askpass pkg-config curl \
    # Install libs for customizing Nerves systems
    && apt-get -y install libssl-dev libncurses5-dev bc m4 unzip cmake rsync cpio libnl-3-dev\
    #
    # Optional networking tools
    && apt-get -y install nmap iputils-ping \
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean -y \ 
    && rm -rf /var/lib/apt/lists/* \
    # ~/.hex and ~/.mix end up being owned by root for some reason? Take it back
    && chown -R $USERNAME:$USERNAME /home/$USERNAME/

# Install fwup (https://github.com/fhunleth/fwup)
ENV FWUP_VERSION="1.14.0"
RUN wget https://github.com/fwup-home/fwup/releases/download/v${FWUP_VERSION}/fwup_${FWUP_VERSION}_amd64.deb && \
    apt-get install -y ./fwup_${FWUP_VERSION}_amd64.deb && \
    rm ./fwup_${FWUP_VERSION}_amd64.deb && \
    rm -rf /var/lib/apt/lists/*

USER vscode
RUN mix local.hex --force \
    && mix local.rebar --force \ 
    && mix archive.install hex nerves_bootstrap --force \
    && mix archive.install hex phx_new --force
# Switch back to dialog for any ad-hoc use of apt-get
ENV DEBIAN_FRONTEND=dialog

ENV HOME /home/vscode