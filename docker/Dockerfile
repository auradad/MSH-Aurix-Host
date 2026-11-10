# Aurix Host - MSH runtime image.
# Wraps a stock Pterodactyl "yolks" Java image and bakes in the MSH launcher so server owners cannot bypass it.
# Build one tag per Java version via the BASE build-arg (see .github/workflows/images.yml).
ARG BASE=ghcr.io/pterodactyl/yolks:java_21
FROM ${BASE}

USER root

# The launcher and a fallback MSH.jar live outside /home/container, so the server owner's file manager
# (which only reaches /home/container) can neither see nor change them.
RUN mkdir -p /opt/msh
COPY MSH.jar      /opt/msh/MSH.jar
COPY msh-boot     /usr/local/bin/msh-boot
RUN chmod 0755 /usr/local/bin/msh-boot \
 && chmod 0644 /opt/msh/MSH.jar \
 && chown root:root /usr/local/bin/msh-boot /opt/msh/MSH.jar

# yolks images run the server as the unprivileged `container` user; keep that.
USER container
