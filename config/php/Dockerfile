FROM php:7.2-apache-stretch

COPY config/php.ini /usr/local/etc/php/
#COPY conf/vhost.conf /etc/apache2/sites-available/vhost.conf

#ssl
#COPY ssl/fullchain.pem /etc/apache2/ssl/fullchain.pem
#COPY ssl/privkey.pem /etc/apache2/ssl/privkey.pem

#vhosts
RUN a2enmod ssl
RUN a2enmod rewrite
#RUN a2ensite vhost
RUN service apache2 restart

#enva przekazac w docker compose
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

RUN apt-get update  \
    && apt-get -y upgrade \
    && apt-get install -y zlib1g-dev libicu-dev libpng-dev g++ \
    && apt-get install -y git zip gnupg \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-install gd \
    && docker-php-ext-install zip \
    && docker-php-ext-install pdo pdo_mysql

RUN apt-get update && apt-get install -y libc-client-dev libkrb5-dev && rm -r /var/lib/apt/lists/*
RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl && docker-php-ext-install imap

RUN pecl install redis && pecl install xdebug && docker-php-ext-enable redis xdebug


RUN apt-get update && apt-get install -y xvfb libfontconfig wkhtmltopdf

WORKDIR /var/www/html

#RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
#RUN php -r "if (hash_file('sha384', 'composer-setup.php') === '93b54496392c062774670ac18b134c3b3a95e5a5e5c8f1a9f115f203b75bf9a129d5daa8ba6a13e2cc8a1da0806388a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
#RUN php composer-setup.php
#RUN php -r "unlink('composer-setup.php');"

RUN curl -sL https://deb.nodesource.com/setup_10.x | bash

RUN apt-get install -y build-essential nodejs

RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

RUN apt-get install -y yarn

RUN yarn install

#RECZNIE
# yarn encore production && php composer.phar install