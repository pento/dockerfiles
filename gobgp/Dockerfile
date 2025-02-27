FROM golang:1.18.1-alpine3.15@sha256:42d35674864fbb577594b60b84ddfba1be52b4d4298c961b46ba95e9fb4712e8 AS build

WORKDIR /go/src/github.com/osrg/gobgp/

ARG BUILD_VERSION
ARG ARCHIVE_URL=https://github.com/osrg/gobgp/archive/

ENV CGO_ENABLED 0

RUN test -n "${BUILD_VERSION}" \
	&& apk update \
	&& apk add --no-cache ca-certificates curl gcc musl-dev \
	&& update-ca-certificates \
	&& curl -L "${ARCHIVE_URL}${BUILD_VERSION}.tar.gz" -o /tmp/gobgp.tar.gz \
	&& tar xzf /tmp/gobgp.tar.gz --strip 1 -C /go/src/github.com/osrg/gobgp \
	&& go build -v -trimpath -ldflags="-s -w" ./cmd/gobgp/ \
	&& go build -v -trimpath -ldflags="-s -w" ./cmd/gobgpd/ 

WORKDIR /config


# ----------------------------------------------------------------------------


FROM scratch

LABEL org.opencontainers.image.authors="Jauder Ho <jauderho@users.noreply.github.com>"
LABEL org.opencontainers.image.url="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.documentation="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.source="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.title="jauderho/gobgp"
LABEL org.opencontainers.image.description="GoBGP is an open source BGP implementation"

COPY --from=build /etc/passwd /etc/group /etc/
COPY --from=build /etc/ssl/certs /etc/ssl/certs

COPY --from=build /go/src/github.com/osrg/gobgp/gobgp /usr/local/bin/
COPY --from=build /go/src/github.com/osrg/gobgp/gobgpd /usr/local/bin/
COPY --from=build --chown=nobody:nogroup /config /config

EXPOSE 179
USER nobody

#ENTRYPOINT ["/usr/local/bin/gobgpd"]
#CMD ["-f", "/config/gobgp.toml"]

CMD ["/usr/local/bin/gobgpd", "-f", "/config/gobgp.toml"]
