ARG TEDDYCLOUD_VERSION="v0.6.4"

# Use official TeddyCloud upstream image to fetch binary
FROM ghcr.io/toniebox-reverse-engineering/teddycloud:${TEDDYCLOUD_VERSION}_debian AS teddycloud

# hadolint ignore=DL3006
FROM ${BUILD_FROM} 

EXPOSE 80 443 8443

# Copy files from the build environment
COPY --from=teddycloud /teddycloud/data/www/ /teddycloud/data/www/
COPY --from=teddycloud /teddycloud/*.sh /usr/local/bin/
COPY --from=teddycloud /usr/local/bin/teddycloud /usr/local/bin/teddycloud

# Install necessary runtime dependencies
RUN apt-get update \ 
    && apt-get upgrade -qq \
    && apt-get install -y --no-install-recommends \
        libubsan1=14.2.0-4ubuntu2~24.04 \
        strace=6.8-0ubuntu2 \
        ffmpeg=7:6.1.1-3ubuntu5 \
        curl=8.5.0-2ubuntu10.6 \
        ca-certificates=20240203 \
        nginx=1.24.0-2ubuntu7.3 \
    && rm -rf /var/lib/apt/lists/* \
    && update-ca-certificates \
    && mkdir -p /teddycloud/data/ /teddycloud/data/www/ \
    && ln -s /data/teddycloud/content /teddycloud/data/content \
    && ln -s /data/teddycloud/library /teddycloud/data/library \
    && ln -s /data/teddycloud/custom_img /teddycloud/data/www/custom_img \
    && ln -s /data/teddycloud/firmware /teddycloud/data/firmware \
    && ln -s /data/teddycloud/cache /teddycloud/data/cache \
    && ln -s /config/teddycloud/certs /teddycloud/certs \
    && ln -s /config/teddycloud/config /teddycloud/config


# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Manuel Rueger <manuel@rueg.eu>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Manuel Rueger <manuel@rueg.eu>>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
