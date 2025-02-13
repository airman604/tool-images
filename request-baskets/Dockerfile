# Adapted from official https://github.com/darklynx/request-baskets/blob/master/docker/multistage/Dockerfile

# Stage 1. Building service
FROM golang:latest AS builder
ARG VERSION=v1.2.3

WORKDIR /go/src/rbaskets

RUN apt-get update && apt-get install -y git
RUN git clone --branch ${VERSION} --depth 1 https://github.com/darklynx/request-baskets.git .
RUN GIT_VERSION="$(git describe --dirty='*' || git symbolic-ref -q --short HEAD)" \
  && GIT_COMMIT="$(git rev-parse HEAD)" \
  && GIT_COMMIT_SHORT="$(git rev-parse --short HEAD)" \
  && set -x \
  && CGO_ENABLED=0 GOOS=linux GOARCH=arm64 go build -a -installsuffix cgo \
    -ldflags="-w -s -X main.GitVersion=${GIT_VERSION} -X main.GitCommit=${GIT_COMMIT} -X main.GitCommitShort=${GIT_COMMIT_SHORT}" \
    -o /go/bin/rbaskets

# Stage 2. Packaging into alpine
FROM alpine:latest
RUN apk --no-cache add ca-certificates
VOLUME /var/lib/rbaskets
COPY --from=builder /go/src/rbaskets/docker/entrypoint.sh /bin/entrypoint
COPY --from=builder /go/bin/rbaskets /bin/rbaskets
EXPOSE 55555
CMD ["/bin/entrypoint"]
