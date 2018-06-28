FROM debian:stretch-slim as base

RUN  apt-get -yqq update && \
     apt-get install -yq --no-install-recommends \
     ca-certificates \
     expat \
     libgomp1 \
     libva-drm1\
     libva1 && \
     apt-get autoremove -y && \
     apt-get clean -y
