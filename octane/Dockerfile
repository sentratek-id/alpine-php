# alpine:3.20 ships php83-8.3.15, which predates the PHP2Shell fix
# (8.3.36). 3.23 and 3.24 carry php83-8.3.36-r0 / php84-8.4.27-r0.
FROM alpine:3.24

LABEL org.opencontainers.image.source=https://github.com/sentratek-id/alpine-php

ENV TERM=linux
ENV TZ=UTC

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apk update \
    && apk add tzdata bash curl ca-certificates sed zip unzip git sqlite libcap libpng \
        php83-fpm php83-soap php83-openssl php83-gmp php83-pdo_odbc php83-json php83-pear \
        php83-dom php83-pdo php83-zip php83-mysqli php83-sqlite3 php83-apcu php83-imap \
        php83-pdo_pgsql php83-bcmath php83-gd php83-odbc php83-pdo_mysql php83-mbstring \
        php83-pdo_sqlite php83-gettext php83-xmlreader php83-bz2 php83-iconv php83-intl \
        php83-pdo_dblib php83-curl php83-ctype php83-session php83-tokenizer php83-dev \
        php83-fileinfo php83-pcntl php83-posix php83-xmlwriter \
        php83-pecl-igbinary php83-pecl-redis php83-pecl-swoole \
        runuser composer \
    && pecl83 channel-update https://pecl.php.net/channel.xml \
    && composer global require --quiet --no-ansi laravel/envoy \
    && ln -s /root/.composer/vendor/laravel/envoy/bin/envoy /usr/local/bin/envoy \
    # cleaning
    && composer clear-cache --quiet \
    && apk cache clean \
    # PHP2Shell fail-closed gate (CVE-2026-109618 / CVE-2026-109619):
    # abort the build unless the runtime is at least the fixed release of
    # its own branch (8.2>=.35, 8.3>=.36, 8.4>=.27, 8.5>=.12)
    && php83 -r '$f=["8.2"=>"8.2.35","8.3"=>"8.3.36","8.4"=>"8.4.27","8.5"=>"8.5.12"][$b=PHP_MAJOR_VERSION.".".PHP_MINOR_VERSION]??""; printf("PHP runtime: %s\n", PHP_VERSION); exit($f!=="" && version_compare(PHP_VERSION, $f, ">=") ? 0 : 42);'

RUN sed -i "s|;*date.timezone =.*|date.timezone = ${TZ}|i" /etc/php83/php.ini

# PHP2Shell hardening: the session.upload_progress producer writes
# attacker-controlled filenames into native session files even on patched
# PHP (the emergency fix covers consumers only). Disable it permanently.
COPY php2shell.ini /etc/php83/conf.d/php2shell.ini

COPY runner.sh /runner.sh

# Add a non-root user to prevent files being created with root permissions on host machine.
ONBUILD ARG USER_CONTAINER=worker
ONBUILD ENV USER_CONTAINER=${USER_CONTAINER}

ONBUILD RUN adduser -D ${USER_CONTAINER} ${USER_CONTAINER}

EXPOSE 80

WORKDIR /var/www

ARG DOCKER_ENV=production
ENV DOCKER_ENV=${DOCKER_ENV}
ENV DOCKER_WORKERS=auto

CMD ["/runner.sh"]

