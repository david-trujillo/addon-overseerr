ARG BUILD_FROM=ghcr.io/hassio-addons/base:17.2.5
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Argumentos de build (puedes cambiar la versión de Overseerr si lo deseas)
ARG OVERSEERR_VERSION="1.33.2"
ARG COMMIT_TAG="dev"

# Instala dependencias necesarias
RUN apk add --no-cache \
    build-base \
    python3 \
    yarn \
    curl \
    tar

# Descarga y descomprime Overseerr
RUN mkdir -p /opt \
    && curl -J -L -o /tmp/overseerr.tar.gz "https://github.com/sct/overseerr/archive/v${OVERSEERR_VERSION}.tar.gz" \
    && tar zxvf /tmp/overseerr.tar.gz --strip 1 -C /opt

# Instala dependencias y compila Overseerr
WORKDIR /opt
RUN CYPRESS_INSTALL_BINARY=0 yarn --frozen-lockfile --network-timeout 1000000 \
    && yarn build \
    && yarn install --production --ignore-scripts --prefer-offline --network-timeout 1000000 \
    && yarn cache clean

# Guarda el commit tag
RUN echo "{\"commitTag\": \"${COMMIT_TAG}\"}" > "/opt/committag.json"

# Limpieza final
RUN apk del --no-cache build-base \
    && rm -rf /opt/.next/cache /opt/server /opt/src /tmp/*

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
    maintainer="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
