# See https://github.com/docker-library/php/blob/4677ca134fe48d20c820a19becb99198824d78e3/7.0/fpm/Dockerfile
FROM php:7.0-fpm

MAINTAINER Chris Diehl <cultclassik@gmail.com>

RUN apt-get update && apt-get install -y \
  nano \
  git \
  unzip \
  libmcrypt-dev \
  libldap2-dev \
  libldb-dev \
  libicu-dev \
  libxml2-dev \
  zlib1g-dev \
  unixODBC-dev

# fix up ldap libs
RUN ln -s /usr/lib/x86_64-linux-gnu/libldap.so /usr/lib/libldap.so \
  && ln -s /usr/lib/x86_64-linux-gnu/liblber.so /usr/lib/liblber.so

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer --version

# Set timezone
RUN rm /etc/localtime
RUN ln -s /usr/share/zoneinfo/America/Chicago /etc/localtime
RUN "date"

# Type docker-php-ext-install to see available extensions
# might not be needed: RUN docker-php-ext-install mysqlnd

#RUN ln -s /usr/include /usr/local/incl \
#  && docker-php-ext-configure odbc --with-unixODBC=shared,/usr


# ugly stuff needed to get the unix odbc working, which is needed to talk to mssql server
RUN docker-php-ext-configure odbc --with-unixODBC=shared,/usr; exit 0

RUN set -x \
  && cd /usr/src/php/ext/odbc \
  && phpize \
  && sed -ri 's@^ *test +"\$PHP_.*" *= *"no" *&& *PHP_.*=yes *$@#&@g' configure \
  && ./configure --with-unixODBC=shared,/usr \
  && docker-php-ext-install odbc

#RUN docker-php-ext-install odbc

RUN docker-php-ext-install \
  pdo \
  pdo_mysql \
  mcrypt \
  ldap \
  intl \
  opcache \
  soap \
  xml \
  zip \
  mbstring

# RUN docker-php-ext-install mysqlnd

COPY config/php.ini /usr/local/etc/php

RUN echo 'alias sf="php app/console"' >> ~/.bashrc
RUN echo 'alias sf3="php bin/console"' >> ~/.bashrc

WORKDIR /var/www/symfony
