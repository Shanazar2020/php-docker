FROM php:8.2-fpm-alpine as app

# php extension installer, copy binary into container
COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/local/bin/

# exit on errors set,
RUN set -eux; \
    install-php-extensions pdo pdo_mysql

# RUN docker-php-ext-install pdo pdo_mysql

# super user to use composer from terminal as docker
ENV COMPOSER_ALLOW_SUPERUSER=1

# composer from docker-hub image
COPY --from=composer:2.4 /usr/bin/composer /usr/bin/composer

# user composer cache to reduce docker build time
# unchaged parts are taken from cache directly instead of building again
COPY ./app/composer.* ./

# install
RUN  composer install --prefer-dist --no-dev --no-scripts --no-progress --no-interaction

# copy app files to wd
COPY ./app .

RUN composer dump-autoload --optimize

FROM app as app_dev

ENV XDEBUG_MODE=off

COPY ./php/conf.d/xdebug.ini $PHP_INI_DIR/conf.d/

RUN set -eux; \
    install-php-extensions xdebug