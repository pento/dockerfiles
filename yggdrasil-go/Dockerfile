FROM golang:1.18.1-alpine3.15@sha256:42d35674864fbb577594b60b84ddfba1be52b4d4298c961b46ba95e9fb4712e8 AS build

WORKDIR /go/src/github.com/yggdrasil-network/yggdrasil-go/

ARG BUILD_VERSION
ARG ARCHIVE_URL=https://github.com/yggdrasil-network/yggdrasil-go/archive/
ARG PKGSRC=github.com/yggdrasil-network/yggdrasil-go/src/version

ENV CGO_ENABLED 0

RUN test -n "${BUILD_VERSION}" \
	&& apk update \
	&& apk add --no-cache ca-certificates curl gcc musl-dev \
	&& update-ca-certificates \
	&& curl -L "${ARCHIVE_URL}${BUILD_VERSION}.tar.gz" -o /tmp/yggdrasil-go.tar.gz \
	&& tar xzf /tmp/yggdrasil-go.tar.gz --strip 1 -C /go/src/github.com/yggdrasil-network/yggdrasil-go \
	&& go build -o ./yggdrasil -v -trimpath -ldflags="-s -w -X ${PKGSRC}.buildName=yggdrasil -X ${PKGSRC}.buildVersion=${BUILD_VERSION}" ./cmd/yggdrasil \
	&& go build -o ./yggdrasilctl -v -trimpath -ldflags="-s -w -X ${PKGSRC}.buildName=yggdrasil -X ${PKGSRC}.buildVersion=${BUILD_VERSION}" ./cmd/yggdrasilctl \
	&& go build -o ./genkeys -v -trimpath -ldflags="-s -w -X ${PKGSRC}.buildName=yggdrasil -X ${PKGSRC}.buildVersion=${BUILD_VERSION}" ./cmd/genkeys/main.go

WORKDIR /config


# ----------------------------------------------------------------------------


FROM scratch
#FROM alpine:3.15.4@sha256:4edbd2beb5f78b1014028f4fbb99f3237d9561100b6881aabbf5acce2c4f9454

LABEL org.opencontainers.image.authors="Jauder Ho <jauderho@users.noreply.github.com>"
LABEL org.opencontainers.image.url="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.documentation="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.source="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.title="jauderho/yggdrasil-go"
LABEL org.opencontainers.image.description="yggdrasil is an implementation of a fully end-to-end encrypted network"

COPY --from=build /etc/ssl/certs /etc/ssl/certs

COPY --from=build /go/src/github.com/yggdrasil-network/yggdrasil-go/yggdrasil /usr/local/bin/yggdrasil
COPY --from=build /go/src/github.com/yggdrasil-network/yggdrasil-go/yggdrasilctl /usr/local/bin/yggdrasilctl
COPY --from=build /go/src/github.com/yggdrasil-network/yggdrasil-go/genkeys /usr/local/bin/genkeys
COPY --from=build /config /config

CMD ["/usr/local/bin/yggdrasil"]

