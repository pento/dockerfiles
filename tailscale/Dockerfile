FROM golang:1.18.1-alpine3.15@sha256:42d35674864fbb577594b60b84ddfba1be52b4d4298c961b46ba95e9fb4712e8 AS build

WORKDIR /go/src/github.com/tailscale/tailscale

ARG BUILD_VERSION
ARG ARCHIVE_URL=https://github.com/tailscale/tailscale/archive/

ENV GO111MODULE on
ENV CGO_ENABLED 0

COPY . .

RUN test -n "${BUILD_VERSION}" \
	&& apk update \
	&& apk add --no-cache ca-certificates curl git gcc build-base \
	&& update-ca-certificates \
	&& curl -L "${ARCHIVE_URL}${BUILD_VERSION}.tar.gz" -o /tmp/tailscale.tar.gz \
	&& tar xzf /tmp/tailscale.tar.gz --strip 1 -C /go/src/github.com/tailscale/tailscale \ 
	&& go build -v -trimpath -buildmode=pie -tags xversion -ldflags="-s -w -X tailscale.com/version.Short=${BUILD_VERSION}" tailscale.com/cmd/tailscale \
	&& go build -v -trimpath -buildmode=pie -tags xversion -ldflags="-s -w -X tailscale.com/version.Short=${BUILD_VERSION}" tailscale.com/cmd/tailscaled 

# Validation check
RUN cp tailscale /go/bin/tailscale
RUN cp tailscaled /go/bin/tailscaled
RUN tailscale version


# ----------------------------------------------------------------------------


FROM alpine:3.15.4@sha256:4edbd2beb5f78b1014028f4fbb99f3237d9561100b6881aabbf5acce2c4f9454

LABEL org.opencontainers.image.authors="Jauder Ho <jauderho@users.noreply.github.com>"
LABEL org.opencontainers.image.url="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.documentation="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.source="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.title="jauderho/tailscale"
LABEL org.opencontainers.image.description="Tailscale is Wireguard made easy"

RUN apk add --no-cache ca-certificates iptables ip6tables iproute2 \
	&& update-ca-certificates \
  && rm -rf /var/cache/apk/*

COPY --from=build /go/bin/tailscale /usr/local/bin
COPY --from=build /go/bin/tailscaled /usr/local/bin

WORKDIR /tailscale

CMD ["tailscaled"]

