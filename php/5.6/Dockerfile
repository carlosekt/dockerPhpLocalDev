FROM php:5.6-fpm-stretch

RUN apt-get clean && apt-get update && apt-get install -y \
    cron \
    sendmail \
    libmcrypt-dev \
    libicu-dev \
    libfreetype6-dev libjpeg62-turbo-dev \
    libxml2-dev \
    libssl-dev \
    mysql-client \
    curl libcurl3 libcurl3-dev \
    openssl \
    libzip-dev \
    unzip \
    git \
    libmemcached-dev \
    imagemagick \
    libmagickwand-dev \
    zlib1g-dev \
    gnupg \
        --no-install-recommends

RUN docker-php-ext-install gd intl pdo pdo_mysql sockets soap mysqli zip curl
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/lib/x86_64-linux-gnu/ --with-jpeg-dir=/usr/lib/x86_64-linux-gnu/

# Phalcon
RUN git clone -b 'v3.4.1' --single-branch --depth 1 https://github.com/phalcon/cphalcon /usr/local/src/cphalcon ;\
    cd /usr/local/src/cphalcon/build && ./install ;\
    echo "extension=phalcon.so" > /usr/local/etc/php/conf.d/phalcon.ini

#Redis
RUN pecl install redis-3.1.0 \
    && docker-php-ext-enable redis

#Memcached
RUN pecl install memcached-2.2.0 \
    && docker-php-ext-enable memcached

# Install composer && global asset plugin
ENV COMPOSER_HOME /root/.composer
ENV PATH /root/.composer/vendor/bin:$PATH
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && /usr/local/bin/composer global require "fxp/composer-asset-plugin" \
    && /usr/local/bin/composer global require "laravel/installer"

#XDebug
RUN pecl install xdebug-2.5.5 \
    && docker-php-ext-enable xdebug

RUN usermod -u 1000 www-data

EXPOSE 9000