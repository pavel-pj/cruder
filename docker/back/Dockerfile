FROM php:8.4-fpm-alpine

# ОБНОВИТЬ репозитории ПЕРЕД установкой
RUN apk update && apk upgrade

# 1. Устанавливаем ОЧЕНЬ МИНИМАЛЬНЫЙ набор для Laravel
RUN apk add --no-cache \
    libzip-dev \
    libpng-dev \
    oniguruma-dev \
    postgresql-dev \
    curl \
    git \
    unzip

# 2. Только необходимые PHP-расширения для Laravel
RUN docker-php-ext-install \
    pdo_pgsql \
    bcmath \
    zip \
    gd \
    opcache

# 3. Composer (самая легкая установка)
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# 6. Рабочая директория
WORKDIR /var/www/html

# 7. Простой entrypoint
#COPY docker-entrypoint.sh /usr/local/bin/
#RUN chmod +x /usr/local/bin/docker-entrypoint.sh

# 8. Пользователь для разработки
ARG UID=1000
ARG GID=1000

RUN addgroup -g ${GID} -S www \
    && adduser -u ${UID} -S www -G www

# 9. Права на папки Laravel
RUN mkdir -p /var/www/html/storage/logs \
    /var/www/html/storage/framework/views \
    /var/www/html/storage/framework/sessions \
    /var/www/html/storage/framework/cache \
    /var/www/html/bootstrap/cache \
    && chown -R www:www /var/www/html \
    && chmod -R 775 /var/www/html/storage \
    && chmod -R 775 /var/www/html/bootstrap/cache

# 10. Меняем пользователя в PHP-FPM
RUN sed -i 's/user = www-data/user = www/g' /usr/local/etc/php-fpm.d/www.conf \
    && sed -i 's/group = www-data/group = www/g' /usr/local/etc/php-fpm.d/www.conf

USER www

#ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["php-fpm"]