# FFMPEG & Golang binding for Go Media Framework (GMF)

## What's inside?

In this repo you'll find the following images:

 - base (something that you don't need but used across all other images)
 - <ffmpeg-version>-build (ffmpeg compiled from source with all plugins available, you don't need that as well)
 - <ffmpeg-version>-golang-<golang-version> (golang + ffmpeg build environment)
 - <ffmpeg-version>-runtime (ffmpeg runtime for golang compiled application)
 - <ffmpeg-version>-openjdk-<jdk-version> (ffmpeg runtime for openjdk compiled application)
 - test (test image with one of the Go Media Framework applications complied and executed as the verification that all images work fine)

## How to build a GMF application?

Check the following GMF application:

```dockerfile
FROM ghcr.io/denismakogon/ffmpeg-debian:5.0.1-golang-1 as build-stage
RUN go get github.com/3d0c/gmf
WORKDIR $GOPATH/src/github.com/3d0c/gmf/examples
RUN mkdir -p /examples/tmp && \
    cp tmp/big_buck_bunny.webm /examples/tmp/big_buck_bunny.webm
RUN go build -o /examples/video-to-frames video-to-jpeg-avio.go


FROM ghcr.io/denismakogon/ffmpeg-debian:5.0.1-runtime
COPY --from=build-stage /examples /examples
WORKDIR /examples
ENTRYPOINT ["./video-to-frames"]
```

In this dockerfile:

 - you need `denismakogon/ffmpeg-debian:5.0.1-golang-1` as a build environment for GMF-based applications
 - you need `denismakogon/ffmpeg-debian:5.0.1-runtime` as a runtime for complied application from the build stage
 - compile a binary at the build stage
 - copy binary through docker multi-stage feature to the runtime

```shell
docker build -t test-ffmpeg --build-arg "FFMPEG_VERSION=5.0.1" --build-arg "GOLANG_VERSION=1" -f test/Dockerfile .
```

## How to build docker images

base image:
```shell
docker build -t ghcr.io/denismakogon/ffmpeg-debian:base -f base/Dockerfile .
```

FFMPEG build stage image:
```shell
docker build -t ghcr.io/denismakogon/ffmpeg-debian:5.0.1-build -f ffmpeg/5.0.1/Dockerfile .
```

optional build arg is `BASE_IMAGE_DIGEST_TAG`, it used to point to a specific version of [ffmpeg-debian:base](https://ghcr.io/denismakogon/ffmpeg-debian:base) image.

```shell
docker build -t ghcr.io/denismakogon/ffmpeg-debian:5.0.1-build -f ffmpeg/5.0.1/Dockerfile \
  --build-arg "BASE_IMAGE_DIGEST_TAG=ghcr.io/denismakogon/ffmpeg-debian@sha256:f6781e831ef68a7ff7170374639be85098b49c6a7740a3652d2f8f1ba43afc1e" \
  .
```

see the [following documentation](https://github.com/denismakogon/ffmpeg-debian/pkgs/container/ffmpeg-debian/22132531?tag=sha256-f6781e831ef68a7ff7170374639be85098b49c6a7740a3652d2f8f1ba43afc1e.sig).

runtime image:
```shell
docker build -t ghcr.io/denismakogon/ffmpeg-debian:5.0.1-runtime -f runtime/Dockerfile \
    --build-arg "BASE_IMAGE_DIGEST_TAG=ghcr.io/denismakogon/ffmpeg-debian@sha256:f6781e831ef68a7ff7170374639be85098b49c6a7740a3652d2f8f1ba43afc1e" \
    --build-arg "BUILD_IMAGE_DIGEST_TAG=ghcr.io/denismakogon/ffmpeg-debian@sha256:2b421ef4be773ce80d3f82717d9fd845e6cc03dfad4c961b2886b7a59117a609" \
      .
```

Golang image:
```shell
docker build -t ghcr.io/denismakogon/ffmpeg-debian:5.0.1-golang-1 -f golang/Dockerfile \
  --build-arg "BUILD_IMAGE_DIGEST_TAG=ghcr.io/denismakogon/ffmpeg-debian@sha256:2b421ef4be773ce80d3f82717d9fd845e6cc03dfad4c961b2886b7a59117a609" \
  --build-arg "GOLANG_VERSION=1" \
  .
```
Please note, golang version must in ffmpeg image must match to `GOLANG_VERSION` build arg.

OpenJDK image:
```shell
docker build -t ghcr.io/denismakogon/ffmpeg-debian:5.0.1-openjdk-18 -f openjdk/Dockerfile \
  --build-arg "BASE_IMAGE_DIGEST_TAG=ghcr.io/denismakogon/ffmpeg-debian@sha256:f6781e831ef68a7ff7170374639be85098b49c6a7740a3652d2f8f1ba43afc1e" \
  --build-arg "BUILD_IMAGE_DIGEST_TAG=ghcr.io/denismakogon/ffmpeg-debian@sha256:2b421ef4be773ce80d3f82717d9fd845e6cc03dfad4c961b2886b7a59117a609" \
  --build-arg "JDK_PKG_URL=https://download.java.net/java/GA/jdk18.0.1.1/65ae32619e2f40f3a9af3af1851d6e19/2/GPL/openjdk-18.0.1.1_linux-x64_bin.tar.gz" \
  .
```

```shell
docker build -t ghcr.io/denismakogon/ffmpeg-debian:5.0.1-openjdk-19-ea-panama -f openjdk/Dockerfile \
  --build-arg "FFMPEG_VERSION=5.0.1" \
  --build-arg "JDK_PKG_URL=https://download.java.net/java/early_access/panama/1/openjdk-19-panama+1-13_linux-x64_bin.tar.gz" .
```
