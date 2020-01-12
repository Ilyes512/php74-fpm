FROM php:7.4.1-fpm-buster as runtime

WORKDIR /var/www

    # install deps
    # hadolint ignore=SC2086
RUN apt-get update \
    && apt-get install --assume-yes --no-install-recommends \
        ca-certificates \
        openssl \
        curl \
        git \
        msmtp \
        # dependency of the php intl-extension
        libicu63 \
        # dependency of the php gd-extension
        libpng16-16 \
        libwebp6 \
        libjpeg62-turbo \
        libfreetype6 \
        # dependency of php zip-extension
        libzip4 \
        # dependency of php uv-extension
        libuv1 \
    # install packages that are needed for building php extensions
    && apt-get install --assume-yes --no-install-recommends \
        $PHPIZE_DEPS \
        # dependency of the php intl-extension
        libicu-dev \
        # dependencies of php gd-extension
        libpng-dev \
        libwebp-dev \
        libjpeg62-turbo-dev \
        libfreetype6-dev \
        # dependency of php zip-extension
        libzip-dev \
        # dependency of php uv-extension
        libuv1-dev \
    # configure php gd-extension
    && docker-php-ext-configure gd \
        --enable-gd \
        --with-jpeg \
        --with-freetype \
        --with-webp \
    # install php extensions
    && docker-php-ext-install -j "$(nproc --all)" \
        pdo_mysql \
        intl \
        opcache \
        pcntl \
        gd \
        bcmath \
        zip \
    && pecl install channel://pecl.php.net/uv-0.2.4 \
    && docker-php-ext-enable uv \
    # purge packages that where only needed for building php extensions
    && apt-get purge --assume-yes \
        $PHPIZE_DEPS \
        libicu-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libzip-dev \
        libuv1-dev \
    # remove all (non-hidden) files and dirs in /var/www/
    && rm -rf /var/www/* \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/*

ARG VCS_REF
ARG VERSION
LABEL org.opencontainers.image.revision=$VCS_REF
LABEL org.opencontainers.image.version=$VERSION
LABEL org.opencontainers.image.title=php74-fpm
LABEL org.opencontainers.image.description="A PHP 7.4 based base image for development and production"
LABEL org.opencontainers.image.url=https://github.com/Ilyes512/docker-php74-fpm
LABEL org.opencontainers.image.documentation=https://github.com/Ilyes512/docker-php74-fpm/blob/master/README.md
LABEL org.opencontainers.image.vendor="ilyes512"
LABEL org.opencontainers.image.licenses=MIT
LABEL org.opencontainers.image.source=https://github.com/Ilyes512/docker-php74-fpm

FROM runtime as builder

ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_VERSION 1.9.1
ENV COMPOSER_SHA256 1f210b9037fcf82670d75892dfc44400f13fe9ada7af9e787f93e50e3b764111
ENV XDEBUG_VERSION 2.9.0
ENV PATH /root/.composer/vendor/bin:$PATH

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

    # install composer and xdebug
    # hadolint ignore=SC2086
RUN curl -fsSL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version="$COMPOSER_VERSION" \
    && echo "$COMPOSER_SHA256 */usr/local/bin/composer" | sha256sum -c - \
    && composer --ansi --version --no-interaction \
    # install prestissimo composer plugin (paralell downloading of packages - remove this when Composer 2 is released!)
    && composer global require hirak/prestissimo:0.3.9 \
    # Needed for xdebug extension configuration
    && apt-get update \
    && apt-get install --assume-yes --no-install-recommends \
        $PHPIZE_DEPS \
    && pecl install xdebug-$XDEBUG_VERSION \
    && docker-php-ext-enable xdebug \
    && apt-get purge --assume-yes $PHPIZE_DEPS \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/*

ARG VCS_REF
ARG VERSION
LABEL org.opencontainers.image.revision=$VCS_REF
LABEL org.opencontainers.image.version=$VERSION
LABEL org.opencontainers.image.title=php74-fpm
LABEL org.opencontainers.image.description="A PHP 7.4 based base image for development and production"
LABEL org.opencontainers.image.url=https://github.com/Ilyes512/docker-php74-fpm
LABEL org.opencontainers.image.documentation=https://github.com/Ilyes512/docker-php74-fpm/blob/master/README.md
LABEL org.opencontainers.image.vendor="ilyes512"
LABEL org.opencontainers.image.licenses=MIT
LABEL org.opencontainers.image.source=https://github.com/Ilyes512/docker-php74-fpm
