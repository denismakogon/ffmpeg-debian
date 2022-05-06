FROM debian:bullseye-slim as base

WORKDIR     /tmp/workdir

RUN  apt-get -yqq update && \
     apt-get install -yq --no-install-recommends \
     curl \
     ca-certificates \
     expat \
     libgomp1 \
     libva-drm2 \
     libva2 && \
     apt-get autoremove -y && \
     apt-get clean -y
