ARG BUILD_IMAGE_DIGEST_TAG="ghcr.io/denismakogon/ffmpeg-debian:5.0.1-build"
ARG GOLANG_VERSION=1

FROM ${BUILD_IMAGE_DIGEST_TAG} as build-stage

FROM docker.io/library/golang:${GOLANG_VERSION}-bullseye

RUN apt-get update -y && apt-get install -y --no-install-recommends \
    libva-drm2 \
    libva2 \
    build-essential \
    g++ \
    gcc \
    git \
    libavutil-dev \
    libavformat-dev \
    libavdevice-dev \
    && \
	rm -rf /var/lib/apt/lists/*

COPY --from=build-stage /usr/local /usr/local/
COPY --from=build-stage /opt/ffmpeg/lib/pkgconfig /usr/local/lib/pkgconfig

ENV LD_LIBRARY_PATH=/usr/local/lib
ENV PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
