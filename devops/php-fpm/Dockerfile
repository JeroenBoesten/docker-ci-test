FROM php:8.2-fpm-alpine AS dev

WORKDIR /var/www

# Install system dependencies and PHP extensions required
RUN apk add --update --no-cache \
    libpng \
    libjpeg-turbo \
    freetype \
    libzip \
    icu-libs \
    oniguruma \
    && apk add --no-cache --virtual .build-deps \
    zlib-dev \
    libpng-dev \
    libjpeg-turbo-dev \
    freetype-dev \
    libzip-dev \
    icu-dev \
    oniguruma-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install pdo pdo_mysql gd zip intl mbstring \
    && apk del .build-deps

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Copy the composer json and lock file
COPY composer.json composer.lock ./

# Install the composer dependencies
RUN composer install --no-scripts --no-autoloader

# Copy the code into the container
COPY . .

# Generate the autoload files
RUN composer dump-autoload --optimize

# Set the appropriate permissions for Laravel directories
RUN chown -R www-data:www-data /var/www/storage /var/www/bootstrap/cache

FROM dev

RUN composer install --no-dev --optimize-autoloader
