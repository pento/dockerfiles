FROM alpine:3.15.4@sha256:4edbd2beb5f78b1014028f4fbb99f3237d9561100b6881aabbf5acce2c4f9454

WORKDIR /opt/log4j-scan

ARG BUILD_VERSION
ARG GIT_URL=https://github.com/fullhunt/log4j-scan.git

#ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PIP_NO_CACHE_DIR=1

# requirements.txt now generated from Pipfile
# pipenv lock -r > requirements.txt
#COPY requirements.txt . 

RUN \
	apk update && \
	build_pkgs="build-base libffi-dev openssl-dev python3-dev git curl" && \
	runtime_pkgs="ca-certificates openssl python3 py3-pip" && \
	apk --no-cache add --virtual build-dependencies ${build_pkgs} && \
	apk --no-cache add ${runtime_pkgs} && \
	ln -s /usr/bin/python3 /usr/bin/python && \
	python3 -m pip install --no-cache-dir --upgrade pip && \
	pip3 install --no-cache-dir --upgrade wheel && \
	git clone --depth 1 ${GIT_URL} --branch ${BUILD_VERSION} /opt/log4j-scan && \
	#git clone --depth 1 ${GIT_URL} /opt/log4j-scan && \
	cd /opt/log4j-scan && \
	pip3 install --no-cache-dir -r requirements.txt && \
	apk del build-dependencies && \
	rm -rf /var/cache/apk/* && \
	rm -rf /root/.cache/pip && \
	rm -rf /root/.cargo

LABEL org.opencontainers.image.authors="Jauder Ho <jauderho@users.noreply.github.com>"
LABEL org.opencontainers.image.url="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.documentation="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.source="https://github.com/jauderho/dockerfiles"
LABEL org.opencontainers.image.title="jauderho/log4j-scan"
LABEL org.opencontainers.image.description="A fully automated, accurate, and extensive scanner for finding vulnerable log4j hosts"

ENTRYPOINT ["python","/opt/log4j-scan/log4j-scan.py"]
CMD ["-h"]
