FROM php:7.1-apache

MAINTAINER Rafael Silveira <rsilveiracc@gmail.com>

ARG UID=1000

ENV APP=/var/www/html
ENV WP_CORE_DIR=$APP

RUN a2enmod rewrite expires

# Copy a new .ini to php conf.d/ folder
COPY ./application.ini /usr/local/etc/php/conf.d/application.ini

RUN apt-get update && apt-get install -y --no-install-recommends \
  libfreetype6-dev \
  libjpeg62-turbo-dev \
  libpng12-dev \
  libmemcached-dev \
  zlib1g-dev \
  vim \
  wget \
  sendmail \
  && apt-get clean; rm -rf /var/lib/apt/lists/* \

  # Install & configure PHP extensions
  && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
  && docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
  && docker-php-ext-configure mysqli --with-mysqli=mysqlnd \
  && docker-php-ext-install gd pdo_mysql mysqli \

  # Install Xdebug
  && pecl install xdebug \
  && docker-php-ext-enable xdebug

# Install PHPUnit and XDebug
RUN cd /tmp && wget --quiet https://phar.phpunit.de/phpunit.phar \
  && chmod +x phpunit.phar \
  && mv phpunit.phar /usr/local/bin/phpunit

COPY ./ $APP/

RUN curl -sS https://getcomposer.org/installer | php -- \
  --install-dir=/usr/local/bin --filename=composer

RUN usermod -u ${UID:-1000} www-data \
  && chown -R www-data:www-data $APP/*

VOLUME $APP