FROM php:8.2-fpm-buster

ARG TIMEZONE
ARG WITH_XDEBUG=false

COPY php.ini /usr/local/etc/php/conf.d/docker-php-config.ini

RUN apt-get update && apt-get install -y \
    gnupg \
    g++ \
    procps \
    openssl \
    git \
    unzip \
    zlib1g-dev \
    libzip-dev \
    libfreetype6-dev \
    libpng-dev \
    libjpeg-dev \
    libicu-dev  \
    libonig-dev \
    libxslt1-dev \
    acl \
    && echo 'alias sf="php bin/console"' >> ~/.bashrc

RUN docker-php-ext-configure gd --with-jpeg --with-freetype 

RUN docker-php-ext-install \
    pdo pdo_mysql zip xsl gd intl opcache exif mbstring

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone \
    && printf '[PHP]\ndate.timezone = "%s"\n', ${TIMEZONE} > /usr/local/etc/php/conf.d/tzone.ini \
    && "date"

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN if [ "$WITH_XDEBUG" = "true" ]; then \
        apt-get install -y wget git unzip; \
        pecl install xdebug; \
        docker-php-ext-enable xdebug; \
        echo "xdebug.start_with_request=yes" >> /usr/local/etc/php/conf.d/xdebug.ini; \
        echo "xdebug.mode=debug" >> /usr/local/etc/php/conf.d/xdebug.ini; \
        echo "xdebug.discover_client_host=0" >> /usr/local/etc/php/conf.d/xdebug.ini; \
        echo "xdebug.client_port=9003" >> /usr/local/etc/php/conf.d/xdebug.ini; \
        echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/conf.d/xdebug.ini; \
    fi ;

WORKDIR /var/www/symfony