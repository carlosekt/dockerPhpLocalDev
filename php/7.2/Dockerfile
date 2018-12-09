FROM php:7.2-fpm-stretch

RUN apt-get clean && apt-get update -yqq && apt-get install -y apt-utils \
    cron \
    sendmail \
    libmcrypt-dev \
    libicu-dev \
    libfreetype6-dev libjpeg62-turbo-dev libpng-dev \
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
    osslsigncode \
    wget \
    nano \
        --no-install-recommends

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/
RUN docker-php-ext-install gd intl pdo pdo_mysql sockets soap mysqli zip curl

# Phalcon
RUN git clone -b 'v3.4.2' --single-branch --depth 1 https://github.com/phalcon/cphalcon /usr/local/src/cphalcon ;\
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
    && /usr/local/bin/composer global require "hirak/prestissimo" \
    && /usr/local/bin/composer global require "fxp/composer-asset-plugin" \
    && /usr/local/bin/composer global require "laravel/installer"

# Xdebug
RUN pecl install -o -f xdebug \
    && docker-php-ext-enable xdebug
COPY ./xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# Fake sendmail
#RUN echo "sendmail_path = /var/www/sendmail/fake_sendmail.sh" > /usr/local/etc/php/conf.d/fake_sendmail.ini

# Config
RUN echo "error_log = /var/log/php-fpm/php-fpm.log \nlog_errors = On \nerror_reporting = E_ERROR | E_PARSE \ndisplay_errors = On" >> /usr/local/etc/php/conf.d/log.ini \
    && sed -i 's/;catch_workers_output.*/catch_workers_output = yes/g' /usr/local/etc/php-fpm.d/www.conf \
    && sed -i 's/;php_admin_value\[error_log\].*/php_admin_value\[error_log\] = \/var\/log\/php-fpm\/php-fpm.log/g' /usr/local/etc/php-fpm.d/www.conf \
    && sed -i 's/;php_admin_flag\[log_errors\].*/php_admin_flag\[log_errors\] = on/g' /usr/local/etc/php-fpm.d/www.conf \
    && sed -i 's/pm.max_children = .*/pm.max_children = 200/g' /usr/local/etc/php-fpm.d/www.conf \
    && sed -i 's/pm.start_servers = .*/pm.start_servers = 10/g' /usr/local/etc/php-fpm.d/www.conf \
    && sed -i 's/pm.min_spare_servers = .*/pm.min_spare_servers = 10/g' /usr/local/etc/php-fpm.d/www.conf \
    && sed -i 's/pm.max_spare_servers = .*/pm.max_spare_servers = 100/g' /usr/local/etc/php-fpm.d/www.conf \
    && sed -i 's/pm = .*/pm = dynamic/g' /usr/local/etc/php-fpm.d/www.conf

#Fake sendmail
RUN curl -Lsf 'https://storage.googleapis.com/golang/go1.11.2.linux-amd64.tar.gz' | tar -C '/usr/local' -xvzf -
ENV PATH /usr/local/go/bin:$PATH
RUN go get github.com/mailhog/mhsendmail
RUN cp /root/go/bin/mhsendmail /usr/bin/mhsendmail
RUN echo 'sendmail_path = /usr/bin/mhsendmail --smtp-addr mailhog:1025' > /usr/local/etc/php/conf.d/fake_sendmail.ini

# Logs
RUN mkdir -p /var/log/php-fpm \
    && chown -R www-data:www-data /var/log/php-fpm

RUN usermod -u 1000 www-data

EXPOSE 9000