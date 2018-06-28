FROM denismakogon/ffmpeg-debian:build-stage as build-stage

FROM golang:1.10.3-stretch

COPY --from=build-stage /usr/local /usr/local/
COPY --from=build-stage /opt/ffmpeg/lib/pkgconfig /usr/local/lib/pkgconfig

RUN apt-get update -y && apt-get install -y --no-install-recommends \
    libva-drm1 \
    libva1 \
    build-essential \
    g++ \
    gcc \
    git \
    libavutil-dev \
    libavformat-dev \
    libavdevice-dev \
    && \
	rm -rf /var/lib/apt/lists/*

ENV LD_LIBRARY_PATH=/usr/local/lib
ENV PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
