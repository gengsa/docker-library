FROM php:5.6.36-fpm-alpine3.7

ENV PHALCON_VERSION=2.0.13

ENV MEMCACHED_DEPS zlib-dev libmemcached-dev cyrus-sasl-dev

ENV YAML_DEPS yaml-dev

ENV GD_DEPS libpng-dev

# set -x can Print commands and their arguments as they are executed.
RUN set -x \
    # update mirror
    && sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories \
    && apk update \
    \
    # $PHPIZE_DEPS from parent image
    # include: autoconf dpkg-dev dpkg file g++ gcc libc-dev make pkgconf re2c, and it's deps
    && apk add --no-cache --update --virtual .phpize-deps $PHPIZE_DEPS \
    && apk add --no-cache --update --virtual .gd-deps $GD_DEPS \
    \
    # install php pdo_mysql extension
    && docker-php-ext-install \
      gd \
      pdo_mysql \
      zip \
    \
    # install php memcached extension
    && apk add --no-cache --update --virtual .memcached-deps $MEMCACHED_DEPS \
    && pecl install memcached-2.2.0 \
    && docker-php-ext-enable memcached \
    \
    # install php yaml extension
    && apk add --no-cache --update --virtual .yaml-deps $YAML_DEPS \
    && pecl install yaml-1.3.1 \
    && docker-php-ext-enable yaml \
    \
    # install php xdebug extension
    && pecl install xdebug-2.5.5 \
    && docker-php-ext-enable xdebug \
    \
    # install php phalcon extension
    && curl -sSL "https://codeload.github.com/phalcon/cphalcon/tar.gz/phalcon-v2.0.13" | tar -xz \
    && cd cphalcon-phalcon-v2.0.13/build \
    && sh install \
    && cp ../unit-tests/ci/phalcon.ini $(php-config --configure-options | grep -o "with-config-file-scan-dir=\([^ ]*\)" | awk -F'=' '{print $2}') \
    && cd ../../ \
    && rm -r cphalcon-phalcon-v2.0.13 \
    \
    # install composer
    && curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    \
    # remove build deps, tmps
    && apk del .phpize-deps \
    && rm -rf /tmp/* \
    && rm -rf /var/cache/apk/*
# CMD ["/bin/bash"]
