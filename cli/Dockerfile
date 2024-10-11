FROM alpine:3.20

LABEL org.opencontainers.image.source=https://github.com/sentratek-id/alpine-php

ENV TERM=linux
ENV TZ=UTC

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apk update \
    && apk add tzdata bash curl ca-certificates sed zip unzip git sqlite libcap libpng \
        php83-soap php83-openssl php83-gmp php83-pdo_odbc php83-json \
        php83-dom php83-pdo php83-zip php83-mysqli php83-sqlite3 php83-apcu \
        php83-pdo_pgsql php83-bcmath php83-gd php83-odbc php83-pdo_mysql \
        php83-pdo_sqlite php83-gettext php83-xmlreader php83-bz2 php83-iconv \
        php83-pdo_dblib php83-curl php83-ctype php83-session php83-tokenizer composer \
    && composer global require --quiet --no-ansi laravel/envoy \
    && composer clear-cache --quiet \
    && ln -s /root/.composer/vendor/laravel/envoy/bin/envoy /usr/local/bin/envoy

RUN sed -i "s|;*date.timezone =.*|date.timezone = ${TZ}|i" /etc/php83/php.ini

