ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base:7.8.3
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

###############################################################################
# Build and install Network UPS Tools 2.8.3 from source (EcoFlow support)
###############################################################################
ARG NUT_VERSION=2.8.3

# 1. Install build dependencies (≈150 MB, later removed)
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        build-essential \
        curl \
        pkg-config \
        libssl-dev \
        libusb-1.0-0-dev \
        libsnmp-dev \
        libsystemd-dev \
        usbutils

# 2. Fetch, compile, and install NUT
RUN curl -fsSL https://networkupstools.org/source/2.8/nut-${NUT_VERSION}.tar.gz \
        | tar xz -C /tmp && \
    cd /tmp/nut-${NUT_VERSION} && \
    ./configure --prefix=/usr \
                --sysconfdir=/etc/nut \
                --with-usb --with-snmp --with-openssl --with-systemd \
                --without-doc && \
    make -j"$(nproc)" && \
    make install-strip && \
    ldconfig && \
    cd / && rm -rf /tmp/nut-${NUT_VERSION}

# 3. Remove build toolchain to keep the final image lean
RUN apt-get purge -y --auto-remove \
        build-essential curl pkg-config \
        libssl-dev libusb-1.0-0-dev libsnmp-dev libsystemd-dev && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Optional: show NUT version in the build log
RUN /usr/sbin/upsdrvctl -V || true

# Copy root filesystem (unchanged)
COPY rootfs /

###############################################################################
# Labels (unchanged)
###############################################################################
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Dale Higgs <dale3h@addons.community>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Dale Higgs <dale3h@addons.community>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
