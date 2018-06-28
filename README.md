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

 - you need `denismakogon/ffmpeg-debian:golang` as a build environment for GMF-based applications
 - you need `denismakogon/ffmpeg-debian:runtime` as a runtime for complied application from the build stage
 - compile a binary at the build stage
 - copy binary through docker multi-stage feature to the runtime

