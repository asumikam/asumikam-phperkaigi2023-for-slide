FROM php:8.1-apache-buster

WORKDIR /app

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

RUN curl -sS https://getcomposer.org/installer | php && \
php -r "readfile('https://getcomposer.org/installer');" | php && \
mv composer.phar /usr/local/bin/composer

COPY my_app/composer.* /app/

RUN composer install \
    --no-dev \
    --ignore-platform-reqs \
    --no-interaction \
    --no-plugins \
    --no-scripts \
    --optimize-autoloader \
    --prefer-dist

COPY my_app/ /app

COPY docker/config/000-default-override.conf /etc/apache2/sites-available/000-default.conf
COPY docker/config/apache2-override.conf /etc/apache2/apache2.conf
