FROM php:7.1-fpm-alpine

ENV OHRM_VERSION 4.3
ENV OHRM_MD5 d27e3f22a484b0e7103f08d43187f94e

RUN set -ex \
	&& apk add --no-cache --virtual .build-deps \
		coreutils \
		freetype-dev \
		libjpeg-turbo-dev \
		libpng-dev \
		unzip \
	\
	&& cd .. && rm -r html \
	&& curl -fSL -o orangehrm.zip "https://sourceforge.net/projects/orangehrm/files/stable/${OHRM_VERSION}/orangehrm-${OHRM_VERSION}.zip/download" \
	&& echo "${OHRM_MD5} orangehrm.zip" | md5sum -c - \
	&& unzip -q orangehrm.zip "orangehrm-${OHRM_VERSION}/*" \
	&& mv orangehrm-$OHRM_VERSION html \
	&& rm orangehrm.zip \
	&& chown www-data:www-data html \
	&& chown -R www-data:www-data html/symfony/cache html/symfony/log \
	&& chmod -R 775 html/symfony/cache html/symfony/log \
	\
	&& docker-php-ext-configure gd \
		--with-freetype-dir=/usr/include/ \
		--with-jpeg-dir=/usr/include/ \
		--with-png-dir=/usr/include/ \
	&& docker-php-ext-install -j "$(nproc)" \
		gd \
		opcache \
		pdo_mysql \
		zip \
	&& runDeps="$( \
		scanelf --needed --nobanner --format '%n#p' --recursive /usr/local \
			| tr ',' '\n' \
			| sort -u \
			| awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
	)" \
	&& apk add --virtual .phpexts-deps $runDeps \
	&& apk del .build-deps

RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=60'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini;
