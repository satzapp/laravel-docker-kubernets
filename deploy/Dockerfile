FROM php:7.2-fpm-alpine

LABEL user = "satzapplications@gmail.com"

RUN apk add --no-cache --virtual .build-deps \
    $PHPIZE_DEPS \
    curl-dev \
    imagemagick-dev \
    libtool \
    libxml2-dev \
    postgresql-dev \
    sqlite-dev \
    && apk add --no-cache \
    curl \
    git \
    imagemagick \
    mysql-client \
    postgresql-libs \
    libintl \
    icu \
    icu-dev \
    && pecl install imagick \
    && docker-php-ext-enable imagick \
    && docker-php-ext-install \
    curl \
    iconv \
    mbstring \
    pdo \
    pdo_mysql \
    pdo_pgsql \
    pdo_sqlite \
    pcntl \
    tokenizer \
    xml \
    zip \
    intl

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer

RUN apk del -f .build-deps

RUN apk update && apk add nano

RUN apk add supervisor

RUN apk add nginx  && \
    rm -rf /var/lib/apt/lists/*

COPY . /var/www/html

WORKDIR /var/www/html

RUN cd /var/www/html 

RUN composer update

# RUN rm /etc/nginx/sites-enabled/default

COPY ./deploy/deploy.conf /etc/nginx/conf.d/default.conf

RUN mv /usr/local/etc/php-fpm.d/www.conf /usr/local/etc/php-fpm.d/www.conf.backup
COPY ./deploy/www.conf /usr/local/etc/php-fpm.d/www.conf

RUN usermod -a -G www-data root
RUN chgrp -R www-data storage

RUN chown -R www-data:www-data ./storage
RUN chmod -R 0777 ./storage

RUN ln -s ./secret/.env .env

RUN chmod +x ./deploy/run

ENTRYPOINT ["./deploy/run"]

EXPOSE 80 443