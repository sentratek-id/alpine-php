FROM ghcr.io/sentratek-id/alpine-php/cli:8.3

LABEL org.opencontainers.image.source=https://github.com/sentratek-id/alpine-php

# Install FPM
RUN apk update \
    && apk add php83-fpm \
    && apk cache clean

# PHP-FPM packages need a nudge to make them docker-friendly
COPY overrides.conf /etc/php83/php-fpm.d/z-overrides.conf

# PHP-FPM has really dirty logs, certainly not good for dockerising
# The following startup script contains some magic to clean these up
COPY php-fpm-startup /usr/local/bin/php-fpm
CMD /usr/local/bin/php-fpm

