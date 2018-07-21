FROM php:7.2.7-fpm-alpine3.7

ENV PHALCON_VERSION=3.4.0

ENV MEMCACHED_DEPS zlib-dev libmemcached-dev cyrus-sasl-dev

ENV YAML_DEPS yaml-dev

# set -x can Print commands and their arguments as they are executed.
RUN set -x \
    # update mirror
    && sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories \
    && apk update \
    \
    # $PHPIZE_DEPS from parent image
    # include: autoconf dpkg-dev dpkg file g++ gcc libc-dev make pkgconf re2c, and it's deps
    && apk add --no-cache --update --virtual .phpize-deps $PHPIZE_DEPS \
    \
    # install php pdo_mysql extension
    && docker-php-ext-install pdo_mysql \
    \
    # install php memcached extension
    && apk add --no-cache --update --virtual .memcached-deps $MEMCACHED_DEPS \
    && pecl install memcached \
    && docker-php-ext-enable memcached \
    \
    # install php yaml extension
    && apk add --no-cache --update --virtual .yaml-deps $YAML_DEPS \
    && pecl install yaml \
    && docker-php-ext-enable yaml \
    \
    # install php xdebug extension
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
    \
    # install php phalcon extension
    && curl -sSL "https://codeload.github.com/phalcon/cphalcon/tar.gz/v${PHALCON_VERSION}" | tar -xz \
    && cd cphalcon-${PHALCON_VERSION}/build \
    && sh install \
    && cp ../tests/_ci/phalcon.ini $(php-config --configure-options | grep -o "with-config-file-scan-dir=\([^ ]*\)" | awk -F'=' '{print $2}') \
    && cd ../../ \
    && rm -r cphalcon-${PHALCON_VERSION} \
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
