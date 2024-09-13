FROM php:8.3-fpm

ARG BUILD_ENV=dev
ENV ENV=$BUILD_ENV

RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    zlib1g-dev libpng-dev libjpeg-dev libfreetype6-dev \
    libxml2-dev \
    libzip-dev \
    libpq-dev \
    vim curl debconf subversion git apt-transport-https apt-utils \
    build-essential locales acl mailutils wget nodejs zip unzip \
    gnupg gnupg1 gnupg2 \
    sudo \
    ssh

RUN docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql
RUN docker-php-ext-configure gd --with-freetype --with-jpeg

RUN docker-php-ext-install pdo_mysql pdo_pgsql pgsql gd opcache zip soap intl

RUN docker-php-ext-enable pdo_mysql pdo_pgsql pgsql gd opcache zip soap intl

COPY ./opcache.ini /usr/local/etc/php/conf.d/
COPY ./custom.ini /usr/local/etc/php/conf.d/

COPY ./must_install_xdebug.sh /tmp/
COPY ./xdebug.ini /tmp/xdebug.ini
RUN chmod u+x /tmp/must_install_xdebug.sh && /tmp/must_install_xdebug.sh

RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer
RUN composer self-update 1.9.0
RUN wget --no-check-certificate https://phar.phpunit.de/phpunit-6.5.3.phar && \
    mv phpunit*.phar phpunit.phar && \
    chmod +x phpunit.phar && \
    mv phpunit.phar /usr/local/bin/phpunit
RUN usermod -u 1000 www-data
RUN usermod -a -G www-data root
RUN mkdir -p /var/www
RUN chown -R www-data:www-data /var/www
RUN mkdir -p /var/www/.composer
RUN chown -R www-data:www-data /var/www/.composer

WORKDIR /var/www/project/