ALPINE PHP
--- 

> This repository is a fork of [`digital-entropy/dokar-php`](https://github.com/digital-entropy/dokar-php/blob/main/cli/Dockerfile), except that we use Alpine Linux instead of Ubuntu.
> This is a work in progress, use it at your own risk.

There is 3 variant in this images.

| Variant | Description | Usage |
| --- | --- | --- | 
| Only CLI | Provide only php-cli. This image can be useful to run such as `laravel-horizon, laravel-websocket, tinker`. | `docker pull ghcr.io/sentratek-id/alpine-php/cli:8.0` |
| Only PHP-FPM | Provide php-fpm from php-cli variant. This image listen on `fastcgi 0.0.0.0:9008`. | `docker pull ghcr.io/sentratek-id/alpine-php/fpm:8.0` |
| With Nginx | Production ready php image that use nginx as web server. This image will serve `/var/www/public` directory.   | `docker pull ghcr.io/sentratek-id/alpine-php/nginx:8.0` |


### Example 

#### 1. PHP-NGINX Example
Below is example usage of php-nginx image for docker-compose in our laravel project
```yaml
version: "3.8"
services:
  web:
    image: ghcr.io/sentratek-id/alpine-php/nginx:8.0
    restart: unless-stopped
    ports:
      - "${APP_DOCK_PORT}:80"
    links:
      - db
      - redis
    volumes:
      # laravel comes up with public dir inside workdir, so we will mount only /var/www
      - .:/var/www
  db:
    image: timescale/timescaledb:2.1.0-pg13
    volumes:
      - db_data:/var/lib/postgresql/data
    restart: unless-stopped
    environment:
      POSTGRES_DB: ${DB_DATABASE}
      POSTGRES_USER: ${DB_USERNAME}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
  redis:
    image: redis
    restart: unless-stopped
volumes:
  db_data: {}
```
for run artisan command you can write something like.
```shell
docker-compose run --user=worker web php artisan list
```
