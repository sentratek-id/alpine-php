FROM ghcr.io/sentratek-id/alpine-php/cli:8.4

LABEL org.opencontainers.image.source=https://github.com/sentratek-id/alpine-php

# Install FPM
RUN apk update \
    && apk add php83-fpm \
    && apk cache clean \
    # PHP2Shell fail-closed gate (CVE-2026-109618 / CVE-2026-109619):
    # abort the build unless the runtime is at least the fixed release of
    # its own branch (8.2>=.35, 8.3>=.36, 8.4>=.27, 8.5>=.12)
    && php83 -r '$f=["8.2"=>"8.2.35","8.3"=>"8.3.36","8.4"=>"8.4.27","8.5"=>"8.5.12"][$b=PHP_MAJOR_VERSION.".".PHP_MINOR_VERSION]??""; printf("PHP runtime: %s\n", PHP_VERSION); exit($f!=="" && version_compare(PHP_VERSION, $f, ">=") ? 0 : 42);'

# PHP-FPM packages need a nudge to make them docker-friendly
COPY overrides.conf /etc/php83/php-fpm.d/z-overrides.conf

# PHP-FPM has really dirty logs, certainly not good for dockerising
# The following startup script contains some magic to clean these up
COPY php-fpm-startup /usr/local/bin/php-fpm
CMD /usr/local/bin/php-fpm

