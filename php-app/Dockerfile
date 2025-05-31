# Stage 1: Build the application
FROM php-base:latest AS build

# Create a directory for the application
WORKDIR /var/www/html

# Install node dependencies
COPY package.json .
RUN npm install

# Copy the rest of your application code
COPY . .

# Install dependencies
RUN composer install

# Clear Laravel config cache
# RUN composer dump-autoload -o

# Build your application
RUN npm run build

# Clean up Node.js dependencies to reduce image size
RUN rm -rf node_modules \
    && rm -rf /usr/local/bin/node \
    && rm -rf /home/www-data/.[^.]* \
    && rm -rf /usr/local/lib/node_modules \
    && rm -rf ~/.npm

# Final stage
FROM php-base:latest

# Copy files from build stage
COPY --from=build --chown=www-data:www-data /var/www/html /var/www/html

# Copy conf files
COPY ./config/nginx.conf /etc/nginx/http.d/default.conf
COPY ./config/php-custom.ini /usr/local/etc/php/conf.d/99-custom.ini

# Give permissions to run as non-root (www-data)
RUN mkdir -p /var/lib/nginx/tmp /var/log/nginx /etc/supervisor.d && \
    touch /var/log/supervisord.log && \
    chown -R www-data:www-data /var/lib/nginx /var/log/nginx /run /etc/supervisor.d /etc/supervisord.conf /var/log/supervisord.log && \
    rm -rf /var/cache/apk/*

# Expose webserver port
EXPOSE 80

# Switch to non-root user
USER www-data

# Start services
CMD ["sh", "-c", "php-fpm -D && nginx -g 'daemon off;'"]