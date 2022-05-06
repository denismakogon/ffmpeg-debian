# FFMPEG & Golang binding for Go Media Framework (GMF)

## What's inside?

In this repo you'll find the following images:

 - base (something that you don't need but used across all other images)
 - build-stage (ffmpeg compiled from source with all plugins available, you don't need that as well)
 - golang (golang + ffmpeg build environment)
 - runtime (ffmpeg runtime for golang compiled application)
 - test (test image with one of the Go Media Framework applications complied and executed as the verification that all images work fine)

## How to build a GMF application?

Check the following GMF application:

```dockerfile
FROM denismakogon/ffmpeg-debian:golang as build-stage
RUN go get github.com/3d0c/gmf
WORKDIR $GOPATH/src/github.com/3d0c/gmf/examples
RUN mkdir -p /examples/tmp && \
    cp tmp/big_buck_bunny.webm /examples/tmp/big_buck_bunny.webm
RUN go build -o /examples/video-to-frames video-to-jpeg-avio.go


FROM denismakogon/ffmpeg-debian:runtime
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
docker build -t denismakogon/ffmpeg-debian:base -f base/Dockerfile .
```

FFMPEG build stage image:
```shell
docker build -t denismakogon/ffmpeg-debian:5.0.1-build -f ffmpeg/5.0.1/Dockerfile .
```

runtime image:
```shell
docker build -t denismakogon/ffmpeg-debian:5.0.1-runtime -f runtime/Dockerfile --build-arg "FFMPEG_VERSION=5.0.1" .
```

Golang image:
```shell
docker build -t denismakogon/ffmpeg-debian:5.0.1-golang-1 -f golang/Dockerfile --build-arg "FFMPEG_VERSION=5.0.1" --build-arg "GOLANG_VERSION=1" .
```
Please note, golang version must in ffmpeg image must match to `GOLANG_VERSION` build arg.

OpenJDK image:
```shell
docker build -t denismakogon/ffmpeg-debian:5.0.1-openjdk-18 -f openjdk/Dockerfile \
  --build-arg "FFMPEG_VERSION=5.0.1" \
  --build-arg "JDK_PKG_URL=https://download.java.net/java/GA/jdk18.0.1.1/65ae32619e2f40f3a9af3af1851d6e19/2/GPL/openjdk-18.0.1.1_linux-x64_bin.tar.gz" .
```

```shell
docker build -t denismakogon/ffmpeg-debian:5.0.1-openjdk-19-panama -f openjdk/Dockerfile \
  --build-arg "FFMPEG_VERSION=5.0.1" \
  --build-arg "JDK_PKG_URL=https://download.java.net/java/early_access/panama/1/openjdk-19-panama+1-13_linux-x64_bin.tar.gz" .
```
