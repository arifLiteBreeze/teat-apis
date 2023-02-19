FROM php:7.4-fpm

WORKDIR /app

RUN groupadd -g 1000 -o www &&\
    useradd -m -u 1000 -g 1000 -o -s /bin/bash www &&\
    usermod -aG sudo www

RUN apt-get update &&\
    apt-get install -y libzip-dev &&\
    apt-get install -y gnupg2 &&\
    apt-get install -y libpng-dev libfreetype6-dev libjpeg62-turbo-dev &&\
    apt-get install -y --no-install-recommends supervisor &&\
    rm /etc/apt/preferences.d/no-debian-php && \
    apt-get install -y libxml2-dev php-soap &&\
    apt-get install -y default-mysql-client

COPY ./supervisor/supervisord.conf /etc/supervisor
RUN chgrp -R www /var/log &&\
    chmod -R g+w /var/log

RUN docker-php-ext-install zip &&\
    docker-php-ext-install pdo_mysql &&\
    docker-php-ext-install exif &&\
    docker-php-ext-configure gd --with-freetype --with-jpeg &&\
    docker-php-ext-install -j$(nproc) gd &&\
    docker-php-ext-install soap &&\
    pecl install redis && docker-php-ext-enable redis

COPY ./php/conf.d/custom.ini /usr/local/etc/php/conf.d/custom.ini

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]
