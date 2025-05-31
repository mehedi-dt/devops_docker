FROM node:20.12.2-alpine3.18 AS node

FROM php:8.1.18-fpm-alpine3.18

# Copy Node.js binaries from node image
COPY --from=node /usr/local/ /usr/local/

# Install install-php-extensions helper
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

RUN chmod +x /usr/local/bin/install-php-extensions\
    \
    # Install required PHP extensions and OS deps
    && apk add --no-cache icu-data-full protobuf-dev grpc-dev \
    && install-php-extensions exif intl pcntl gd grpc redis \
    && rm -rf /var/cache/apk/* \
    \
    # Install ZIP extension and its required dependencies
    && apk add --no-cache libzip-dev \
    && docker-php-ext-install -j$(nproc) zip \
    \
    # Install Composer
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer --version=2.2.7 \
    && php -r "unlink('composer-setup.php');" \
    \
    # Install nginx
    && apk add --no-cache nginx \
    \
    # Setting ini file for php from templates (php.ini-production)
    && cp /usr/local/etc/php/php.ini-production /usr/local/etc/php/php.ini \
    \
    && rm -rf /var/cache/apk/*