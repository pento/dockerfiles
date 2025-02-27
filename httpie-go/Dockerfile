FROM golang:1.18.1-alpine3.15@sha256:42d35674864fbb577594b60b84ddfba1be52b4d4298c961b46ba95e9fb4712e8 AS build

WORKDIR /go/src/github.com/nojima/httpie-go/

ARG BUILD_VERSION
ARG ARCHIVE_URL=https://github.com/nojima/httpie-go/archive/
ARG GIT_URL=https://github.com/nojima/httpie-go.git

ENV CGO_ENABLED 0

RUN test -n "${BUILD_VERSION}" \
	&& apk update \
	&& apk add --no-cache git gcc musl-dev \ 
	&& git clone --depth 1 ${GIT_URL} --branch ${BUILD_VERSION} /go/src/github.com/nojima/httpie-go \
	&& cd /go/src/github.com/nojima/httpie-go \
	&& go build -v -trimpath -ldflags="-s -w" ./cmd/ht 

WORKDIR /config


# ----------------------------------------------------------------------------


FROM scratch
#FROM alpine:3.15.0

LABEL org.opencontainers.image.authors="Jauder Ho <jauderho@users.noreply.github.com>"
LABEL org.opencontainers.image.url="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.documentation="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.source="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.title="jauderho/httpie-go"
LABEL org.opencontainers.image.description="httpie-go is a user-friendly HTTP client CLI"

COPY --from=build /go/src/github.com/nojima/httpie-go/ht /usr/local/bin/ht
COPY --from=build /config /config

ENTRYPOINT ["/usr/local/bin/ht"]

