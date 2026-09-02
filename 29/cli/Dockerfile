#
# NOTE: THIS DOCKERFILE IS GENERATED VIA "apply-templates.sh"
#
# PLEASE DO NOT EDIT IT DIRECTLY.
#

FROM alpine:3.24

RUN apk add --no-cache \
		ca-certificates \
# DOCKER_HOST=ssh://... -- https://github.com/docker/cli/pull/1014
		openssh-client \
# https://github.com/docker-library/docker/issues/482#issuecomment-2197116408
		git

# ensure that nsswitch.conf is set up for Go's "netgo" implementation (which Docker explicitly uses)
# - https://github.com/moby/moby/blob/v24.0.6/hack/make.sh#L111
# - https://github.com/golang/go/blob/go1.19.13/src/net/conf.go#L227-L303
# - docker run --rm debian:stretch grep '^hosts:' /etc/nsswitch.conf
RUN [ -e /etc/nsswitch.conf ] && grep '^hosts: files dns' /etc/nsswitch.conf

# pre-add a "docker" group for socket usage
RUN set -eux; \
	addgroup -g 2375 -S docker

ENV DOCKER_VERSION 29.7.2

RUN set -eux; \
	\
	apkArch="$(apk --print-arch)"; \
	case "$apkArch" in \
		'x86_64') \
			url='https://download.docker.com/linux/static/stable/x86_64/docker-29.7.2.tgz'; \
			;; \
		'armhf') \
			url='https://download.docker.com/linux/static/stable/armel/docker-29.7.2.tgz'; \
			;; \
		'armv7') \
			url='https://download.docker.com/linux/static/stable/armhf/docker-29.7.2.tgz'; \
			;; \
		'aarch64') \
			url='https://download.docker.com/linux/static/stable/aarch64/docker-29.7.2.tgz'; \
			;; \
		*) echo >&2 "error: unsupported 'docker.tgz' architecture ($apkArch)"; exit 1 ;; \
	esac; \
	\
	wget -O 'docker.tgz' "$url"; \
	\
	tar --extract \
		--file docker.tgz \
		--strip-components 1 \
		--directory /usr/local/bin/ \
		--no-same-owner \
		'docker/docker' \
	; \
	rm docker.tgz; \
	\
	docker --version

ENV DOCKER_BUILDX_VERSION 0.37.0
RUN set -eux; \
	\
	apkArch="$(apk --print-arch)"; \
	case "$apkArch" in \
		'x86_64') \
			url='https://github.com/docker/buildx/releases/download/v0.37.0/buildx-v0.37.0.linux-amd64'; \
			sha256='ae43fa08c796b44efc86d7a63c55f73f7c35f3101188dea7bf93bcd6f99577ba'; \
			;; \
		'armhf') \
			url='https://github.com/docker/buildx/releases/download/v0.37.0/buildx-v0.37.0.linux-arm-v6'; \
			sha256='56229f2d8459f902ca4cad09e2a931e149ea7e10d8728fe4f0f315d66b76fe2c'; \
			;; \
		'armv7') \
			url='https://github.com/docker/buildx/releases/download/v0.37.0/buildx-v0.37.0.linux-arm-v7'; \
			sha256='9171f1aff0d6f48afd2248b493accc98d4c8d632e40698ad9444f872991acb13'; \
			;; \
		'aarch64') \
			url='https://github.com/docker/buildx/releases/download/v0.37.0/buildx-v0.37.0.linux-arm64'; \
			sha256='d263ce31bd2c9e9210aaa2c7537c67802bccabcd342e4c9fe4907085ddb41aa5'; \
			;; \
		'ppc64le') \
			url='https://github.com/docker/buildx/releases/download/v0.37.0/buildx-v0.37.0.linux-ppc64le'; \
			sha256='ed99c4401ece2c6940d99c24b719332af9dfbbe8aba308c59676183a6285f376'; \
			;; \
		'riscv64') \
			url='https://github.com/docker/buildx/releases/download/v0.37.0/buildx-v0.37.0.linux-riscv64'; \
			sha256='8de7783c3dae7b14b361a20d35b15d9368a31a85791da53e4e96e146c7ab120a'; \
			;; \
		's390x') \
			url='https://github.com/docker/buildx/releases/download/v0.37.0/buildx-v0.37.0.linux-s390x'; \
			sha256='5209e6d229cbaee67ee9a070abf2de96304ffcc2104edcf1475a0d5458f94a50'; \
			;; \
		*) echo >&2 "warning: unsupported 'docker-buildx' architecture ($apkArch); skipping"; exit 0 ;; \
	esac; \
	\
	wget -O 'docker-buildx' "$url"; \
	echo "$sha256 *"'docker-buildx' | sha256sum -c -; \
	\
	plugin='/usr/local/libexec/docker/cli-plugins/docker-buildx'; \
	mkdir -p "$(dirname "$plugin")"; \
	mv -vT 'docker-buildx' "$plugin"; \
	chmod +x "$plugin"; \
	\
	docker buildx version

ENV DOCKER_COMPOSE_VERSION 5.5.0
RUN set -eux; \
	\
	apkArch="$(apk --print-arch)"; \
	case "$apkArch" in \
		'x86_64') \
			url='https://github.com/docker/compose/releases/download/v5.5.0/docker-compose-linux-x86_64'; \
			sha256='c57ab918abd5b05ca7e7d0f275875dd1330a695074f309dc9eab1b49efafcd4b'; \
			;; \
		'armhf') \
			url='https://github.com/docker/compose/releases/download/v5.5.0/docker-compose-linux-armv6'; \
			sha256='fad8dc4c8ab0a80395ccb3dbd3d7da46c85024818dcc7b90305ad6971de61f43'; \
			;; \
		'armv7') \
			url='https://github.com/docker/compose/releases/download/v5.5.0/docker-compose-linux-armv7'; \
			sha256='50a7c5bc659f0d619f71f5600b1f15981b99f86df6167d600e0445ef179d5a06'; \
			;; \
		'aarch64') \
			url='https://github.com/docker/compose/releases/download/v5.5.0/docker-compose-linux-aarch64'; \
			sha256='ff42489f5a9b879d5d117c5ffea6defc27390b3286da8ad52cbc9c6ab5df590e'; \
			;; \
		'ppc64le') \
			url='https://github.com/docker/compose/releases/download/v5.5.0/docker-compose-linux-ppc64le'; \
			sha256='75fc29e87b04b7573c8deb7cf98ea4c2ab58b6c73f74a1d467ecc40485725be9'; \
			;; \
		'riscv64') \
			url='https://github.com/docker/compose/releases/download/v5.5.0/docker-compose-linux-riscv64'; \
			sha256='d782b976282d629b6100477ddd4ed2e0439b0892e3d05d36bc4fc82fa1d6de5b'; \
			;; \
		's390x') \
			url='https://github.com/docker/compose/releases/download/v5.5.0/docker-compose-linux-s390x'; \
			sha256='abf5f4044cec07af2dcf08e90629e95cb8efd61c877f60f02d7c11308cce105d'; \
			;; \
		*) echo >&2 "warning: unsupported 'docker-compose' architecture ($apkArch); skipping"; exit 0 ;; \
	esac; \
	\
	wget -O 'docker-compose' "$url"; \
	echo "$sha256 *"'docker-compose' | sha256sum -c -; \
	\
	plugin='/usr/local/libexec/docker/cli-plugins/docker-compose'; \
	mkdir -p "$(dirname "$plugin")"; \
	mv -vT 'docker-compose' "$plugin"; \
	chmod +x "$plugin"; \
	\
	ln -sv "$plugin" /usr/local/bin/; \
	docker-compose --version; \
	docker compose version

COPY modprobe.sh /usr/local/bin/modprobe
COPY docker-entrypoint.sh /usr/local/bin/

# https://github.com/docker-library/docker/pull/166
#   dockerd-entrypoint.sh uses DOCKER_TLS_CERTDIR for auto-generating TLS certificates
#   docker-entrypoint.sh uses DOCKER_TLS_CERTDIR for auto-setting DOCKER_TLS_VERIFY and DOCKER_CERT_PATH
# (For this to work, at least the "client" subdirectory of this path needs to be shared between the client and server containers via a volume, "docker cp", or other means of data sharing.)
ENV DOCKER_TLS_CERTDIR=/certs
# also, ensure the directory pre-exists and has wide enough permissions for "dockerd-entrypoint.sh" to create subdirectories, even when run in "rootless" mode
RUN mkdir /certs /certs/client && chmod 1777 /certs /certs/client
# (doing both /certs and /certs/client so that if Docker does a "copy-up" into a volume defined on /certs/client, it will "do the right thing" by default in a way that still works for rootless users)

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["sh"]
