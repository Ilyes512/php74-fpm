FROM php:7.4.5-fpm-alpine3.11 as runtime

# Latest version of UV at the moment: https://pecl.php.net/package/uv
ARG PHP_UV_VERSION=0.2.4

WORKDIR /var/www

    # install deps
    # hadolint ignore=SC2086
RUN apk add --no-cache --upgrade \
        ca-certificates \
        openssl \
        curl \
        msmtp \
        # dependency of the php intl-extension
        icu \
        # dependency of the php gd-extension
        libpng \
        libwebp \
        libjpeg-turbo \
        freetype \
        # dependency of php zip-extension
        libzip \
        # dependency of php uv-extension
        libuv \
    # install needed php extension configuration/installing packages
    && apk add --no-cache --upgrade --virtual .build \
        $PHPIZE_DEPS \
        # dependency of the php intl-extension
        icu-dev \
        # dependencies of php gd-extension
        libpng-dev \
        libwebp-dev \
        libjpeg-turbo-dev \
        freetype-dev \
        # dependency of php zip-extension
        libzip-dev \
        # dependency of php uv-extension
        libuv-dev \
    # configure php gd-extension
    && docker-php-ext-configure gd \
        --enable-gd \
        --with-jpeg \
        --with-freetype \
        --with-webp \
    # install php extensions
    && docker-php-ext-install -j "$(getconf _NPROCESSORS_ONLN)" \
        pdo_mysql \
        intl \
        opcache \
        pcntl \
        gd \
        bcmath \
        zip \
    && pecl install "channel://pecl.php.net/uv-$PHP_UV_VERSION" \
    && docker-php-ext-enable uv \
    && cp "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini" \
    && apk del .build \
    # remove all (non-hidden) files and dirs in /var/www/
    && rm -rf /var/www/* \
    && rm -rf /tmp/*

COPY files /

ARG VCS_REF
ARG CREATED
ARG VERSION=$PHP_VERSION
LABEL org.opencontainers.image.revision=$VCS_REF
LABEL org.opencontainers.image.version=$VERSION
LABEL org.opencontainers.image.created=$CREATED
LABEL org.opencontainers.image.title=php74-fpm
LABEL org.opencontainers.image.description="A PHP 7.4 based base image"
LABEL org.opencontainers.image.url=https://github.com/Ilyes512/docker-php74-fpm
LABEL org.opencontainers.image.documentation=https://github.com/Ilyes512/docker-php74-fpm/blob/master/README.md
LABEL org.opencontainers.image.vendor="ilyes512"
LABEL org.opencontainers.image.licenses=MIT
LABEL org.opencontainers.image.source=https://github.com/Ilyes512/docker-php74-fpm

FROM runtime as builder

ENV COMPOSER_ALLOW_SUPERUSER 1
ENV PATH "/root/.composer/vendor/bin:${PATH}"

ARG COMPOSER_VERSION=1.10.6
ARG COMPOSER_SHA256=29bdac1bda34d8902b9f9e4f5816de08879b8f3fafad901e4283519cdefbee7b
ARG XDEBUG_VERSION=2.9.5
ARG PRESTISSIMO_VERSION=0.3.10

SHELL ["/bin/ash", "-eo", "pipefail", "-c"]

    # install composer and xdebug
    # hadolint ignore=SC2086
RUN curl -fsSL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version="$COMPOSER_VERSION" \
    && echo "$COMPOSER_SHA256 */usr/local/bin/composer" | sha256sum -c - \
    && composer --ansi --version --no-interaction \
    # install prestissimo composer plugin (paralell downloading of packages - remove this when Composer 2 is released!)
    && composer global require "hirak/prestissimo:$PRESTISSIMO_VERSION" \
    # Install some builder-only apps
    && apk add --no-cache --upgrade --no-progress \
        vim \
        git \
        unzip \
        sqlite \
    # Needed for xdebug extension configuration (is removed later on, see --virtual)
    && apk add --no-cache --upgrade --virtual .build $PHPIZE_DEPS \
    && pecl install "xdebug-$XDEBUG_VERSION" \
    && docker-php-ext-enable xdebug \
    && cp "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini" \
    && apk del .build \
    && rm -rf /tmp/*

ARG VCS_REF
ARG CREATED
ARG VERSION=$PHP_VERSION
LABEL org.opencontainers.image.revision=$VCS_REF
LABEL org.opencontainers.image.version=$VERSION
LABEL org.opencontainers.image.created=$CREATED
LABEL org.opencontainers.image.title=php74-fpm
LABEL org.opencontainers.image.description="A PHP 7.4 based base image"
LABEL org.opencontainers.image.url=https://github.com/Ilyes512/docker-php74-fpm
LABEL org.opencontainers.image.documentation=https://github.com/Ilyes512/docker-php74-fpm/blob/master/README.md
LABEL org.opencontainers.image.vendor="ilyes512"
LABEL org.opencontainers.image.licenses=MIT
LABEL org.opencontainers.image.source=https://github.com/Ilyes512/docker-php74-fpm
