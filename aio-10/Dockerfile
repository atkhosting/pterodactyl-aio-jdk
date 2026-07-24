# ----------------------------------
# Pterodactyl Core Dockerfile
# Environment: Java 10 (Multi-vendor)
# Minimum Panel Version: 1.7.0
# ----------------------------------

# ---- Malloc implementations builder stage ----
FROM debian:stable-slim AS malloc-builder

# Install build dependencies for both jemalloc and mimalloc
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        build-essential \
        graphviz \
        which \
        lsof \
        curl \
        ca-certificates \
        openssl \
        git \
        tar \
        sqlite3 \
        fontconfig \
        libfreetype6 \
        tzdata \
        iproute2 \
        libstdc++6 \
        autoconf \
        cmake && \
    rm -rf /var/lib/apt/lists/*

RUN mkdir -p /opt /tmp/jprof /tmp/nmt /tmp/pmap /diagnostic

# Build jemalloc with profiling support
WORKDIR /opt/jemalloc
# We manually compile jemalloc here, as installing through apt-get (libjemalloc2) does not enable the profiling options we need in Jemalloc.
# If you look below, we use the --enable-prof flag in the autogen.sh script to enable profiling.
RUN git clone https://github.com/jemalloc/jemalloc.git . && \
    ./autogen.sh --enable-prof && \
    make dist && \
    make && \
    make install

# Build mimalloc for performance, they manually compiled the mimalloc fr
WORKDIR /opt/mimalloc
RUN git clone https://github.com/microsoft/mimalloc.git . && \
    mkdir -p build && \
    cd build && \
    cmake -DMI_BUILD_TESTS=OFF ../ && \
    make install

# get JDK source
FROM azul/zulu-openjdk:10 AS zulu
FROM bellsoft/liberica-openjdk-debian:10 AS liberica

# ---- Builder stage: assemble and strip all JDKs in one place ----
FROM debian:stable-slim AS jdk-builder

RUN mkdir -p /opt/java

COPY --from=zulu      /usr/lib/jvm  /tmp/zulu-jvm
COPY --from=liberica  /usr/lib/jvm  /tmp/liberica-jvm

RUN set -eux && \
    ZULU_DIR=$(find /tmp/zulu-jvm -mindepth 1 -maxdepth 1 -type d | head -n1) && \
    if [ -n "$ZULU_DIR" ]; then \
        mv "$ZULU_DIR" /opt/java/zulu; \
    else \
        echo "ERROR: No Zulu JDK directory found!" && exit 1; \
    fi && \
    rm -rf /tmp/zulu-jvm && \
    \
    LIBERICA_DIR=$(find /tmp/liberica-jvm -mindepth 1 -maxdepth 1 -type d | head -n1) && \
    if [ -n "$LIBERICA_DIR" ]; then \
        mv "$LIBERICA_DIR" /opt/java/liberica; \
    else \
        echo "ERROR: No Liberica JDK directory found!" && exit 1; \
    fi && \
    rm -rf /tmp/liberica-jvm && \
    \
    for jdk in zulu liberica; do \
        if [ ! -f "/opt/java/$jdk/bin/java" ]; then \
            echo "ERROR: /opt/java/$jdk/bin/java not found!" && exit 1; \
        fi; \
        echo "OK /opt/java/$jdk/bin/java"; \
    done && \
    \
    for dir in man demo sample legal jmods include; do \
        find /opt/java -type d -name "$dir" -exec rm -rf {} + 2>/dev/null || true; \
    done && \
    \
    find /opt/java -type f \( -name "src.zip" -o -name "*.debuginfo" -o -name "*.diz" -o -name "*.map" \) -delete 2>/dev/null || true && \
COPY --from=malloc-builder /usr/local/lib/libjemalloc.so /usr/local/lib/libjemalloc.so
COPY --from=malloc-builder /usr/local/lib/libmimalloc.so /usr/local/lib/libmimalloc.so
COPY --from=malloc-builder /usr/local/bin/jeprof /usr/local/bin/jeprof

ENV JDK_VENDOR=zulu

USER        container
ENV         USER=container HOME=/home/container

WORKDIR     /home/container

COPY        ./../scripts/entrypoint.sh /entrypoint.sh

CMD         ["/bin/bash", "/entrypoint.sh"]