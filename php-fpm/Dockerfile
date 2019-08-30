FROM php:7.2.20-fpm-alpine3.10

# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ARG PGID=1000
ARG NON_ROOT_USER=template
ARG NON_ROOT_GROUP=template

USER root

RUN apk update \
	&& apk add --no-cache libpng libpng-dev libjpeg-turbo libjpeg-turbo-dev freetype freetype-dev zlib zlib-dev libxrender vim \
	&& docker-php-ext-install -j$(nproc) mysqli pdo pdo_mysql mbstring zip bcmath \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include \
	&& docker-php-ext-install -j$(nproc) gd

# Revisar local do laravel.ini
ADD ./laravel.ini /usr/local/etc/php/conf.d
ADD ./laravel.pool.conf /usr/local/etc/php-fpm.d/

RUN set -x \
	&& addgroup -g ${PGID} -S ${NON_ROOT_GROUP} \
	&& adduser -u ${PUID} -D -S -G ${NON_ROOT_GROUP} ${NON_ROOT_USER}

#Confidential configuration
ENV DB_HOST ${DB_HOST}
ENV DB_DRIVER ${DB_DRIVER}
ENV DB_PORT ${DB_PORT}
ENV DB_DATABASE ${DB_DATABASE}
ENV DB_USERNAME ${DB_USERNAME}
ENV DB_PASSWORD ${DB_PASSWORD}


USER ${NON_ROOT_USER}

WORKDIR /var/www

EXPOSE 9000

CMD ["php-fpm"]
