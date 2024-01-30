ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base/amd64:7.3.1
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base system
ARG BUILD_ARCH=amd64
ARG VICTORIAMETRICS_VERSION="v1.97.0"
RUN \
    ARCH="${BUILD_ARCH}" \
    && if [ "${BUILD_ARCH}" = "aarch64" ]; then ARCH="arm64"; fi \
    && if [ "${BUILD_ARCH}" = "armv7" ]; then ARCH="arm"; fi \
    \
    && mkdir -p /tmp/victoria-metrics/ \
    && curl -J -L -o /tmp/victoria-metrics.tar.gz \
        "https://github.com/VictoriaMetrics/VictoriaMetrics/releases/download/${VICTORIAMETRICS_VERSION}/victoria-metrics-linux-${ARCH}-${VICTORIAMETRICS_VERSION}.tar.gz" \
    && tar -xf /tmp/victoria-metrics.tar.gz --directory /tmp/victoria-metrics/ \
    && cp /tmp/victoria-metrics/victoria-metrics-prod /usr/local/bin/victoria-metrics \
    && chmod +x /usr/local/bin/victoria-metrics \
    \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        fonts-noto-cjk=1:20220127+repack1-1 \
        libfontconfig1=2.14.1-4 \
        musl=1.2.3-1 \
        nginx=1.22.1-9 \
    \
    && rm -fr \
        /tmp/* \
        /var/{cache,log}/* \
        /var/lib/apt/lists/* \
        /etc/nginx \
    \
    && mkdir -p /var/log/nginx \
    && touch /var/log/nginx/error.log

# Copy root filesystem
COPY rootfs /

# Health check
HEALTHCHECK \
    CMD curl --fail http://127.0.0.1:1337/-/healthy || exit 1

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
    maintainer="Quentin BERTRAND <contact@quentinbertrand.fr>" \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant QBR Add-ons" \
    org.opencontainers.image.authors="Quentin BERTRAND <contact@quentinbertrand.fr>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://github.com/QuentinBtd/hassio-addons" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
