ARG FFMPEG_VERSION=5.0.1

FROM denismakogon/ffmpeg-debian:${FFMPEG_VERSION}-build as build-stage

FROM denismakogon/ffmpeg-debian:base

COPY --from=build-stage /usr/local /usr/local/
COPY --from=build-stage /opt/ffmpeg/lib/pkgconfig /usr/local/lib/pkgconfig

ENV LD_LIBRARY_PATH=/usr/local/lib
ENV PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
