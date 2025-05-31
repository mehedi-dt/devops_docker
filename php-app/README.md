# PHP Application – Dockerized Setup

This repository contains a Dockerized PHP application setup optimized for Laravel or similar PHP frameworks. It uses a two-stage build process to ensure a clean, efficient, and production-ready image. The application is served using NGINX and PHP-FPM, and is built with Node.js support when necessary.

---

## How It Works

This setup follows a multi-stage Docker build process, leveraging a prebuilt base image (`php-base:latest`) that includes all necessary system dependencies such as:

- PHP-FPM and extensions (e.g., intl, gd, grpc, redis, zip)
- Composer
- Node.js and npm
- NGINX
- Alpine Linux (as a lightweight OS base)

The goal is to speed up CI/CD pipelines and reduce build times by separating concerns between a reusable base image and the final application build.

---

## Prerequisites

- Docker installed on your system
- Built base image (`php-base:latest`) available locally or in a container registry

To build the base image:

```bash
cd base-image
docker build -t php-base:latest .
```

---

## Project Structure

```
php-app/
├── Dockerfile                 # Main multi-stage Dockerfile
├── base-image/                # Base image directory
│   └── Dockerfile             # Dockerfile for the reusable PHP/Node environment
├── config/
│   ├── nginx.conf             # NGINX configuration for serving the app
│   └── php-custom.ini         # Custom PHP configuration overrides
└── (Your PHP Source Code)
```

---

## Main Dockerfile Workflow

### Stage 1: Build Stage

- Uses `php-base:latest` as the base
- Sets working directory to `/var/www/html`
- Installs Node dependencies via `npm install`
- Copies the full source code
- Installs PHP dependencies via Composer
- Builds front-end assets using `npm run build`
- Cleans up unnecessary Node/npm files to reduce image size

### Stage 2: Runtime Stage

- Again uses `php-base:latest`
- Copies built source code from the previous stage (owned by `www-data`)
- Applies custom config:
  - NGINX config: `config/nginx.conf`
  - PHP overrides: `config/php-custom.ini`
- Sets permissions for log and runtime directories
- Switches to non-root user `www-data`
- Exposes port 80 (NGINX)
- Starts PHP-FPM and NGINX together as the default command

---

## Custom Configuration

### NGINX

- Serves the application on port `80`
- Can support HTTPS via `X-Forwarded-Proto` headers if placed behind a reverse proxy

### PHP (`php-custom.ini`)

- Add any custom PHP configuration here
- Format must follow valid `.ini` syntax
- Values will override default PHP configuration

---

## Running the Application

### Step 1: Build the Docker Image

```bash
docker build -t my-php-app:latest .
```

### Step 2: Run the Container

```bash
docker run -p 8080:80 my-php-app:latest
```

This exposes NGINX on `localhost:8080`.

---

## Best Practices

- If your application does not need Node.js (e.g., no frontend assets), you can safely remove related `npm` lines.
- For better Docker layer caching, copy `composer.json` and `composer.lock` first and run `composer install` before copying the full source code.
- Source code is expected to be placed in the root of this repository.
- SSL should be terminated at the reverse proxy (e.g., NGINX or Apache running on the host or load balancer).

---

## To Do

- Add `docker-compose.yml` for service orchestration (PHP, DB, Redis, etc.)
- Define environment variables using `.env`
- Add volume support for persistent data

---

This setup is intended to provide a production-grade, containerized PHP environment with minimal overhead and maximum flexibility.