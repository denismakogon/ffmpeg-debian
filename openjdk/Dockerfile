ARG BUILD_IMAGE_DIGEST_TAG="ghcr.io/denismakogon/ffmpeg-debian:5.0.1-build"
ARG RUNTIME_IMAGE_DIGEST_TAG="ghcr.io/denismakogon/ffmpeg-debian:5.0.1-runtime"

FROM ${BUILD_IMAGE_DIGEST_TAG} as build-stage

FROM ${RUNTIME_IMAGE_DIGEST_TAG}

ARG JDK_PKG_URL

ENV JAVA_HOME=/usr/java

RUN set -eux; \
      apt-get update -qy && apt-get install -y --no-install-recommends curl ca-certificates && \
      JDK_PKG_SHA256=$(curl "$JDK_PKG_URL".sha256) ; \
      curl --output /tmp/jdk.tgz "$JDK_PKG_URL" && \
      echo "$JDK_PKG_SHA256 */tmp/jdk.tgz" | sha256sum -c; \
      mkdir -p "$JAVA_HOME"; \
      tar --extract --file /tmp/jdk.tgz --directory "$JAVA_HOME" --strip-components 1 && \
      rm -fr /tmp/jdk.tgz

COPY --from=build-stage /usr/local /usr/local/
COPY --from=build-stage /opt/ffmpeg/lib/pkgconfig /usr/local/lib/pkgconfig

ENV LD_LIBRARY_PATH=/usr/local/lib
ENV PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
ENV PATH=${PATH}:${JAVA_HOME}/bin
