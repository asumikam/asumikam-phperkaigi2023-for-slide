FROM composer:2.0 as vendor

COPY my_app/composer.* /app/

RUN composer install \
    --no-dev \
    --ignore-platform-reqs \
    --no-interaction \
    --no-plugins \
    --no-scripts \
    --optimize-autoloader \
    --prefer-dist

FROM php:8.1-apache-buster

RUN a2enmod rewrite
RUN apt-get update && apt-get install -y \
    bash \
    vim \
    libicu-dev \
    libzip-dev \
    git \
    unzip \
&& rm -rf /var/lib/apt/lists/*

RUN NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) && \
    docker-php-ext-install -j${NPROC} intl opcache pdo_mysql zip
RUN pecl install redis apcu xdebug && \
    docker-php-ext-enable redis

COPY my_app/ /app
COPY --from=vendor /app/vendor/ /app/vendor

COPY docker/config/000-default-override.conf /etc/apache2/sites-available/000-default.conf
COPY docker/config/apache2-override.conf /etc/apache2/apache2.conf

WORKDIR /app




