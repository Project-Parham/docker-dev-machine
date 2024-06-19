FROM php:7.4-apache

ARG WWWGROUP
ARG WWWUSER
ARG NODE_VERSION=16

WORKDIR /var/www/html

RUN apt-get update && apt-get install -y libcurl4-openssl-dev libxml2-dev
RUN apt-get install zlib1g-dev

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd

RUN apt-get update && apt-get install -y libc-client-dev libkrb5-dev && rm -r /var/lib/apt/lists/*
RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install imap

RUN \
    apt-get update && \
    apt-get install libldap2-dev -y && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ && \
    docker-php-ext-install ldap

RUN pecl install xdebug && docker-php-ext-enable xdebug
RUN pecl install grpc && docker-php-ext-enable grpc
RUN docker-php-ext-install bcmath curl exif fileinfo  intl
RUN docker-php-ext-install mysqli pdo pdo_mysql soap

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer
RUN groupadd --force -g $WWWGROUP dev
RUN useradd -ms /bin/bash --no-user-group -g $WWWGROUP -u $WWWUSER dev

RUN usermod -u $WWWUSER dev

RUN apt-get update && apt-get install apt-file -y && apt-file update && apt-get install vim nano -y 
RUN curl -sLS https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm
RUN apt-get update && apt-get install -y libzip-dev
RUN docker-php-ext-install zip
RUN apt-get install -y rsync
RUN a2enmod rewrite
RUN mkdir /var/logs/xdebug -p && touch /var/logs/xdebug/xdebug.log && chown -R dev:dev /var/logs

