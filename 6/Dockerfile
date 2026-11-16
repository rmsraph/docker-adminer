FROM cgr.dev/chainguard/php:8.5-dev AS build

USER root

RUN	apk add --no-cache \
	curl \
	coreutils \
	pax-utils \
	php-8.5-mysqli \
	php-8.5-pdo \
	php-8.5-pdo_mysql \
	php-8.5-pdo_odbc \
	php-8.5-pdo_dblib \
	php-8.5-pdo_pgsql \
	php-8.5-pdo_sqlite \
&&	mkdir -p /etc/php/conf.d /app /runtime-deps \
&&	printf '%s\n' \
	'upload_max_filesize = 128M' \
	'post_max_size = 128M' \
	'memory_limit = 1G' \
	'max_execution_time = 600' \
	'max_input_vars = 5000' \
	> /etc/php/conf.d/0-upload_large_dumps.ini \
&&	lddtree --copy-to-tree /runtime-deps /usr/lib/php/modules/*.so

WORKDIR /app

ENV	ADMINER_VERSION=6.3.0
ENV	ADMINER_DOWNLOAD_SHA256=641e1a31e8cc333a309e202ef9446e1fd38c2069ed5f9ee509b80d70089d0503
ENV	ADMINER_SRC_DOWNLOAD_SHA256=651353bc14d125b548c429cc3ffaa30b97c8b0f86b1b1692138a35a2047fba3a

RUN	set -x \
&&	curl -fsSL https://github.com/vrana/adminer/releases/download/v$ADMINER_VERSION/adminer-$ADMINER_VERSION.php -o index.php \
&&	echo "$ADMINER_DOWNLOAD_SHA256  index.php" |sha256sum -c - \
&&	curl -fsSL https://github.com/vrana/adminer/archive/v$ADMINER_VERSION.tar.gz -o source.tar.gz \
&&	echo "$ADMINER_SRC_DOWNLOAD_SHA256  source.tar.gz" |sha256sum -c - \
&&	tar xzf source.tar.gz --strip-components=1 "adminer-$ADMINER_VERSION/LICENSE" \
&&	rm source.tar.gz

FROM cgr.dev/chainguard/php:8.5

LABEL org.opencontainers.image.title="Adminer"
LABEL org.opencontainers.image.description="Adminer 6 on Chainguard PHP"
LABEL org.opencontainers.image.source="https://github.com/rmsraph/docker-adminer"

COPY	--from=build /runtime-deps/ /
COPY	--from=build /etc/php/conf.d/ /etc/php/conf.d/
COPY	--from=build --chown=php:php /app/ /app/

WORKDIR /app
USER	php
STOPSIGNAL SIGINT
CMD	[ "-S", "[::]:8080", "-t", "/app" ]

EXPOSE 8080
