ARG BUILD_IMAGE_DIGEST_TAG="ghcr.io/denismakogon/ffmpeg-debian:5.0.1-build"
ARG BASE_IMAGE_DIGEST_TAG="ghcr.io/denismakogon/ffmpeg-debian:base"

FROM ${BUILD_IMAGE_DIGEST_TAG} as build-stage

FROM ${BASE_IMAGE_DIGEST_TAG}

COPY --from=build-stage /usr/local /usr/local/
COPY --from=build-stage /opt/ffmpeg/lib/pkgconfig /usr/local/lib/pkgconfig

ENV LD_LIBRARY_PATH=/usr/local/lib
ENV PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
