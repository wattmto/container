# The base is built from rakazo's computer Dockerfile by the workflow.
ARG RAKAZO_BASE=ghcr.io/wattmto/rakazo-computer-base:latest
FROM ${RAKAZO_BASE}

USER root
# The base image's NSS wrapper files are created at runtime, not during build.
ENV LD_PRELOAD="" HOME=/root
ARG NIX_VERSION=2.28.5
RUN apt-get update \
    && apt-get install -y --no-install-recommends xz-utils \
    && rm -rf /var/lib/apt/lists/* \
    && curl -fsSL "https://releases.nixos.org/nix/nix-${NIX_VERSION}/install" -o /tmp/install-nix \
    && mkdir -m 0755 /nix \
    && NIX_CONFIG='build-users-group =' sh /tmp/install-nix --no-daemon --yes \
    && rm /tmp/install-nix \
    && mkdir -p /etc/nix \
    && printf '%s\n' 'sandbox = false' 'build-users-group =' 'experimental-features = nix-command flakes' > /etc/nix/nix.conf \
    && chown -R 1000:1000 /nix \
    && printf '%s\n' 'export PATH=/nix/var/nix/profiles/per-user/root/profile/bin:$PATH' > /etc/profile.d/rakazo-nix.sh

ENV PATH=/nix/var/nix/profiles/per-user/root/profile/bin:${PATH} \
    HOME=/home/rakazo \
    LD_PRELOAD=/usr/local/lib/libnss_wrapper.so
USER 1000:1000
RUN env -u LD_PRELOAD nix --version && env -u LD_PRELOAD nix store ping --store local
