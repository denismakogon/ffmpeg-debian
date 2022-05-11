ARG FFMPEG_VERSION=5.0.1
ARG GOLANG_VERSION=1

FROM ghcr.io/denismakogon/ffmpeg-debian:${FFMPEG_VERSION}-golang-${GOLANG_VERSION} as build-stage

RUN go install github.com/3d0c/gmf@latest
WORKDIR $GOPATH/src/github.com/3d0c/gmf/examples
RUN mkdir -p /examples/tmp && \
    cp tmp/big_buck_bunny.webm /examples/tmp/big_buck_bunny.webm
RUN go build -o /examples/video-to-frames video-to-jpeg-avio.go

# runtime
FROM ghcr.io/denismakogon/ffmpeg-debian:${FFMPEG_VERSION}-runtime

COPY --from=build-stage /examples /examples
WORKDIR /examples
RUN ./video-to-frames
