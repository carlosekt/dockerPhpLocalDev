FROM php:7.1-fpm-stretch

RUN apt-get clean && apt-get update -yqq && apt-get install -y apt-utils \
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
    iproute2 \
    gnupg \
        --no-install-recommends

RUN docker-php-ext-install gd intl pdo pdo_mysql sockets soap mysqli zip curl
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/lib/x86_64-linux-gnu/ --with-jpeg-dir=/usr/lib/x86_64-linux-gnu/

# Phalcon
RUN git clone -b 'v3.4.1' --single-branch --depth 1 https://github.com/phalcon/cphalcon /usr/local/src/cphalcon ;\
    cd /usr/local/src/cphalcon/build && ./install ;\
    echo "extension=phalcon.so" > /usr/local/etc/php/conf.d/phalcon.ini

# Memcached
RUN git clone -b 'php7' --single-branch --depth 1 https://github.com/php-memcached-dev/php-memcached /usr/local/src/memcached ;\
    cd /usr/local/src/memcached && phpize && ./configure --disable-memcached-sasl && make && make install ;\
    echo "extension=memcached.so" > /usr/local/etc/php/conf.d/memcached.ini

#Redis
RUN pecl install redis \
    && docker-php-ext-enable redis

#MongoDB
RUN pecl install mongodb \
    && docker-php-ext-enable mongodb

# Imagick
RUN pecl install imagick \
    && echo "extension=imagick.so" > /usr/local/etc/php/conf.d/imagick.ini

#Opcache
RUN docker-php-ext-install opcache
COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini

# Install composer && global asset plugin
ENV COMPOSER_HOME /root/.composer
ENV PATH /root/.composer/vendor/bin:$PATH
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && /usr/local/bin/composer global require "fxp/composer-asset-plugin" \
    && /usr/local/bin/composer global require "laravel/installer"

# Xdebug
RUN pecl install -o -f xdebug \
    && docker-php-ext-enable xdebug
COPY ./xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# Fake sendmail
RUN echo "sendmail_path = /var/www/sendmail/fake_sendmail.sh" > /usr/local/etc/php/conf.d/fake_sendmail.ini

RUN usermod -u 1000 www-data

EXPOSE 9000