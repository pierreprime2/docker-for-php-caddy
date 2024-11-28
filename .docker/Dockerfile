FROM php:7.2-fpm

LABEL maintainer="Paul Redmond"

# Install dependencies, including curl and tar if missing
RUN apt-get update && apt-get install -y \
    curl \
    tar \
    unzip \
    && apt-get clean

# Set Caddy version
ENV CADDY_VERSION=2.7.4

# Download and install the Caddy .deb package
RUN curl --silent --show-error --fail --location \
    --output caddy_${CADDY_VERSION}_linux_amd64.deb \
    "https://github.com/caddyserver/caddy/releases/download/v${CADDY_VERSION}/caddy_${CADDY_VERSION}_linux_amd64.deb" \
    && dpkg -i caddy_${CADDY_VERSION}_linux_amd64.deb \
    && rm caddy_${CADDY_VERSION}_linux_amd64.deb \
    && caddy version

# Install PHP extensions
RUN docker-php-ext-install mbstring pdo pdo_mysql

# Copy the Caddyfile and application code
COPY .docker/Caddyfile /etc/Caddyfile
COPY . /srv/app/

# Set permissions
WORKDIR /srv/app/
RUN chown -R www-data:www-data /srv/app/storage /srv/app/bootstrap/cache \
    && chmod -R 775 /srv/app/storage /srv/app/bootstrap/cache



# Start both PHP-FPM and Caddy
CMD ["sh", "-c", "chown -R www-data:www-data /srv/app/storage /srv/app/bootstrap/cache && chmod -R 775 /srv/app/storage /srv/app/bootstrap/cache && php-fpm & caddy run --config /etc/Caddyfile --adapter caddyfile"]
