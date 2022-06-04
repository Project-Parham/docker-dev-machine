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
RUN docker-php-ext-install bcmath curl exif fileinfo  intl
RUN docker-php-ext-install mysqli pdo pdo_mysql soap

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php -r "if (hash_file('sha384', 'composer-setup.php') === '55ce33d7678c5a611085589f1f3ddf8b3c52d662cd01d4ba75c0ee0459970c2200a51f492d557530c71c15d8dba01eae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
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
RUN a2enmod rewrite

