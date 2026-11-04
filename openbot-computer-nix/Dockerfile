# The base is the official OpenBot server image, published for linux/amd64 and
# linux/arm64. The runtime options that the OpenBot docs require (seccomp
# profile, shm-size, stop timeout, the /data volume) belong to docker run or
# compose, not to the image.
# https://github.com/nightly-labs/openbot/blob/main/docs/docker.md
ARG OPENBOT_BASE=ghcr.io/nightly-labs/openbot:0.40.0
FROM ${OPENBOT_BASE}

ARG NIX_VERSION=2.28.5
USER root
RUN apt-get update \
    && apt-get install -y --no-install-recommends curl xz-utils \
    && rm -rf /var/lib/apt/lists/* \
    && mkdir -m 0755 /nix \
    && chown 1000:1000 /nix \
    && mkdir -p /etc/nix \
    && printf '%s\n' 'sandbox = false' 'build-users-group =' 'experimental-features = nix-command flakes' > /etc/nix/nix.conf

# Nix since 2.24 keeps its profile, channel and other state in the XDG state
# dir, which defaults to $HOME/.local/state. HOME here is /data, the volume
# with the OpenBot data: a Nix profile there is easy to lose in a restore. Put
# the Nix state next to the store instead, so that a /nix volume keeps all of
# it.
ENV XDG_STATE_HOME=/nix/state
USER 1000
RUN curl -fsSL "https://releases.nixos.org/nix/nix-${NIX_VERSION}/install" -o /tmp/install-nix \
    && NIX_CONFIG='build-users-group =' NIX_INSTALLER_NO_MODIFY_PROFILE=1 sh /tmp/install-nix --no-daemon --yes \
    && rm /tmp/install-nix

# The installer adds nixpkgs-unstable as a channel, so `nix-shell -p` and
# `nix run` resolve nixpkgs without extra configuration.
ENV PATH=/nix/state/nix/profiles/profile/bin:${PATH}
USER 1000
RUN nix --version && nix store info
