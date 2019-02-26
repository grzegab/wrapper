FROM php:7.2-apache-stretch


RUN a2enmod ssl \
    && a2enmod rewrite \
    && service apache2 restart

#setup host name for apache
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

RUN apt-get update && apt-get install -y gnupg2 apt-transport-https

#Setup yarn
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

RUN apt-get update  \
    && apt-get -y upgrade \
    && apt-get install -y zlib1g-dev libicu-dev libpng-dev g++ \
    && apt-get install -y git zip \
    && apt-get install -y build-essential nodejs \
    && apt-get install -y xvfb libfontconfig wkhtmltopdf \
    && apt-get install -y yarn \
    && apt-get install -y libc-client-dev libkrb5-dev \
    && rm -r /var/lib/apt/lists/*

RUN docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-install gd \
    && docker-php-ext-install zip \
    && docker-php-ext-install pdo pdo_mysql

RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl && docker-php-ext-install imap

RUN pecl install redis && pecl install xdebug && docker-php-ext-enable redis xdebug

WORKDIR /var/www

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('sha384', 'composer-setup.php') === '93b54496392c062774670ac18b134c3b3a95e5a5e5c8f1a9f115f203b75bf9a129d5daa8ba6a13e2cc8a1da0806388a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');"

#setup node if needed
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash

WORKDIR /var/www/html