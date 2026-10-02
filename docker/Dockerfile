# Start from the pure PHP-FPM image
FROM php:8.5-fpm

# Set the working directory inside the container
WORKDIR /var/www/html

# (Optional) You can install extensions here, like:
# RUN docker-php-ext-install pdo_mysql

# Copy your custom php.ini settings into the container
COPY docker/php.ini /usr/local/etc/php/conf.d/custom.ini

# Copy your local project files into the container
COPY . /var/www/html/

# Ensure the web server user owns the files
RUN chown -R www-data:www-data /var/www/html