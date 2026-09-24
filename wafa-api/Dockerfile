FROM php:8.4-cli

# Install system dependencies & PHP extensions
RUN apt-get update && apt-get install -y \
    git zip unzip libpng-dev libonig-dev libxml2-dev sqlite3 libsqlite3-dev \
    && docker-php-ext-install pdo pdo_sqlite mbstring exif pcntl bcmath gd

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www

COPY . .

# Ensure we are inside the Laravel directory if nested
RUN if [ -d "wafa-api" ]; then cp -r wafa-api/* . && cp -r wafa-api/.* . 2>/dev/null || true; fi

RUN composer install --no-dev --optimize-autoloader --ignore-platform-reqs

# Ensure required directories exist and permissions are set
RUN mkdir -p storage/framework/views storage/framework/sessions storage/framework/cache bootstrap/cache database
RUN touch database/database.sqlite
RUN chmod -R 777 storage bootstrap/cache database

# Render sets the PORT environment variable dynamically
ENV PORT=10000
EXPOSE 10000

CMD php artisan migrate --force && php artisan serve --host=0.0.0.0 --port=$PORT