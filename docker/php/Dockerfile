FROM php:7.1-fpm

ENV version 2.1

MAINTAINER ffphoenix

# upgrade the container
RUN apt-get update && \
    apt-get upgrade -y

RUN apt-get install -y --force-yes curl git nano zlib1g-dev \
    && docker-php-ext-install zip pdo pdo_mysql \
    && docker-php-ext-enable zip

# Install composer
ENV COMPOSER_ALLOW_SUPERUSER=1
RUN curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    printf "\nPATH=\"~/.composer/vendor/bin:\$PATH\"\n" | tee -a ~/.bashrc

RUN pecl install xdebug-2.5.0 \
    && docker-php-ext-enable xdebug

RUN pecl install -o -f redis \
    &&  rm -rf /tmp/pear

ARG XDEBUG_INI=/usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

RUN echo "xdebug.default_enable = off" >> ${XDEBUG_INI} \
    && echo "xdebug.remote_enable = on" >> ${XDEBUG_INI} \
    && echo "xdebug.remote_autostart = off" >> ${XDEBUG_INI} \
    && echo "xdebug.remote_connect_back = off" >> ${XDEBUG_INI} \
    && echo "xdebug.remote_port = 9001" >> ${XDEBUG_INI} \
    && echo "xdebug.remote_host = 10.254.254.254" >> ${XDEBUG_INI} \
    && echo "xdebug.remote_log = /var/www/docker/php/xdebug.log" >> ${XDEBUG_INI}

# composer plugin for parallel install
RUN composer global require "hirak/prestissimo"

RUN composer global require "laravel/envoy"

RUN composer global require "laravel/installer"

RUN ln -s /var/www/artisan /usr/local/bin/ \
&& ln -s /vendor/bin/phpunit /usr/local/bin/

CMD ["php-fpm"]

WORKDIR /var/www