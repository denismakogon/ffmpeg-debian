# golang build-stage
FROM denismakogon/ffmpeg-debian:golang as build-stage

RUN go get github.com/3d0c/gmf
WORKDIR $GOPATH/src/github.com/3d0c/gmf/examples
RUN mkdir -p /examples/tmp && \
    cp tmp/big_buck_bunny.webm /examples/tmp/big_buck_bunny.webm
RUN go build -o /examples/video-to-frames video-to-jpeg-avio.go

# runtime
FROM denismakogon/ffmpeg-debian:runtime

COPY --from=build-stage /examples /examples
WORKDIR /examples
RUN ./video-to-frames
