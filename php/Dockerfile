FROM php:7.3-fpm-buster AS base_php

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
    apt-utils \
    curl \
    dialog \
    wget \
    libfreetype6-dev \
    libjpeg-dev \
    libmagickwand-dev \
    libsodium-dev \
    libpng-dev \
    libssh2-1-dev \
    libxslt1-dev \
    libzip-dev \
    gettext-base \
    rsync \
    # clean up apt cache
    && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-configure \
  gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/

RUN docker-php-ext-install \
    bcmath \
    bz2 \
    calendar \
    exif \
    gd \
    gettext \
    intl \
    mbstring \
    mysqli \
    opcache \
    pcntl \
    pdo_mysql \
    soap \
    sockets \
    sodium \
    sysvmsg \
    sysvsem \
    sysvshm \
    xsl \
    zip

FROM base_php AS config

ARG USERNAME=app
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
 && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME -d /var/www \
 && usermod -a -G www-data $USERNAME

RUN curl -sS https://getcomposer.org/installer | \
  php -- --version=1.9.0 --install-dir=/usr/local/bin --filename=composer

COPY ./php/php.ini /usr/local/etc/php/
COPY ./php/www.conf /usr/local/etc/php-fpm.d/
COPY ./php/php-fpm.conf /usr/local/etc/

USER app:www-data

VOLUME /var/www

WORKDIR /var/www/html

EXPOSE 9000