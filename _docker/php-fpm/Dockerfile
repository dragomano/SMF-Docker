ARG PHP_VERSION=8.0

FROM phpdockerio/php:${PHP_VERSION}-fpm as backend

ARG PHP_VERSION

RUN apt-get update; \
	apt-get -y --no-install-recommends install \
		php${PHP_VERSION}-bcmath \
		php${PHP_VERSION}-gd \
		php${PHP_VERSION}-imagick \
		php${PHP_VERSION}-intl \
		php${PHP_VERSION}-mysql \
		php${PHP_VERSION}-pgsql \
		php${PHP_VERSION}-memcached \
		php${PHP_VERSION}-redis; \
	apt-get clean; \
	rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

FROM backend as smf

WORKDIR /app

ARG SMF_VERSION

# https://0exclusive.de/smf/smf2.1_nightly_install.zip
RUN set -eux; \
	SMF_VERSION_DASH="${SMF_VERSION//./-}"; \
	curl -s -k -o install.zip https://download.simplemachines.org/index.php/smf_${SMF_VERSION_DASH}_install.zip && \
	unzip install.zip && \
	rm install.zip; \
	chown -R www-data:www-data . && \
	chmod -R 777 .
