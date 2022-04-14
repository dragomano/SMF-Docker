FROM phpdockerio/php:7.4-fpm as backend

WORKDIR "/var/www/html"

RUN apt-get update; \
	apt-get -y --no-install-recommends install \
		php7.4-bcmath \
		php7.4-gd \
		php7.4-imagick \
		php7.4-intl \
		php7.4-mysql \
		php7.4-pgsql \
		php7.4-memcached \
		php7.4-redis; \
	apt-get clean; \
	rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

FROM backend as smf

RUN curl -SL -o install.zip https://0exclusive.de/smf/smf2.1_nightly_install.zip \
	&& unzip install.zip \
	&& rm install.zip; \
	chown -R www-data:www-data . && chmod -R 777 .
