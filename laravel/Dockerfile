#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#
# To edit the 'employer-api' base Image, visit its repository on Github
#    https://github.com/Laradock/php-fpm
#
# To change its version, see the available Tags on the Docker Hub:
#    https://hub.docker.com/r/laradock/php-fpm/tags/
#
# Note: Base Image name format {image-tag}-{php-version}
#

ARG ALPINE_VERSION=3.12
ARG ALPINE_PHP_VERSION=7.4

FROM php:${ALPINE_PHP_VERSION}-fpm-alpine${ALPINE_VERSION}

ARG ALPINE_PHP_VERSION=7.4

LABEL maintainer="Victor Aguilar <victor.aguilar@ciencias.unam.mx>"

# Basic tools
RUN export TZ=America/Mexico_City
RUN apk add --no-cache tzdata
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

COPY php.ini /usr/local/etc/php/php.ini
COPY laravel.ini /usr/local/etc/php/conf.d
COPY xlaravel.pool.conf /usr/local/etc/php-fpm.d/

RUN docker-php-ext-install \
    pdo_mysql

RUN apk add --no-cache shadow && \
    usermod -u 1000 www-data && \
    apk del shadow

#####################################
# Extra extension:
#####################################

RUN apk add --no-cache libzip-dev
RUN docker-php-ext-install zip

#####################################
# Building tools:
#####################################

ARG INSTALL_XDEBUG=false

RUN if [ ${INSTALL_XDEBUG} = true ]; then \
      apk add --no-cache $PHPIZE_DEPS \
      && pecl install xdebug-2.9.6 \
      && docker-php-ext-enable xdebug \
    ;fi

COPY xdebug-${ALPINE_PHP_VERSION}.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN if [ ${INSTALL_XDEBUG} = false ]; then \
      rm /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    ;fi

ARG INSTALL_BUILDING_TOOLS=false
RUN if [ ${INSTALL_BUILDING_TOOLS} = true ]; then \
      php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
      php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    ;fi

RUN if [ ${INSTALL_BUILDING_TOOLS} = true ]; then \
      apk add --no-cache \
        git \
        openssh \
        npm \
    ;fi

# In develop, the most of the time, we will enter to the container as www-data then it need to have prestisimo
# in its user directory.
USER www-data
RUN if [ ${INSTALL_BUILDING_TOOLS} = true ]; then \
      composer global require hirak/prestissimo \
  ;fi
USER root

RUN docker-php-source extract \
    && apk add --no-cache --virtual .phpize-deps-configure $PHPIZE_DEPS \
    && pecl install apcu \
    && docker-php-ext-enable apcu \
    && apk del .phpize-deps-configure \
    && docker-php-source delete

####################################


CMD ["php-fpm"]

EXPOSE 9000
