FROM php:7.3-apache

ARG WWWGROUP
ARG WWWUSER
ARG NODE_VERSION=16

WORKDIR /var/www/html

RUN apt-get update && apt-get install -y libcurl4-openssl-dev libxml2-dev
RUN apt-get install -y libzip-dev zlib1g-dev; docker-php-ext-install zip
RUN apt-get install -y libjpeg-dev libfreetype6-dev
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd

RUN apt-get update && apt-get install -y libc-client-dev libkrb5-dev && rm -r /var/lib/apt/lists/*
RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install imap

RUN \
    apt-get update && \
    apt-get install libldap2-dev -y && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ && \
    docker-php-ext-install ldap

COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/local/bin/
RUN install-php-extensions xdebug @composer
RUN pecl install grpc && docker-php-ext-enable grpc
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
RUN apt-get update && apt-get install -y libzip-dev
RUN docker-php-ext-install zip
RUN apt-get install -y rsync
RUN a2enmod rewrite
RUN mkdir /var/logs/xdebug -p && touch /var/logs/xdebug/xdebug.log && chown -R dev:dev /var/logs

