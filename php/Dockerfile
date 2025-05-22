
FROM composer:2.7 AS builder

WORKDIR /app

COPY composer.json composer.lock ./


RUN composer install \
    --no-dev \
    --no-scripts \
    --no-autoloader \
    --ignore-platform-reqs \
    --prefer-dist \
    --optimize-autoloader \
    --classmap-authoritative


COPY . .


FROM php:8.2-fpm-alpine3.18

WORKDIR /var/www/html

RUN apk add --no-cache \
    libzip \
    libpq \
    && apk add --no-cache --virtual .build-deps \
    $PHPIZE_DEPS \
    zip \
    libzip-dev \
    postgresql-dev \
    && docker-php-ext-install -j$(nproc) \
    pdo \
    pdo_mysql \
    zip \
    && apk del --no-network .build-deps

COPY --from=builder /app .


RUN chown -R www-data:www-data /var/www/html \
    && chmod -R 755 storage bootstrap/cache


ENV APP_ENV=production
ENV APP_DEBUG=false

EXPOSE 9000

CMD ["php-fpm"]