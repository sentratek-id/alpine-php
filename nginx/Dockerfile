FROM ghcr.io/sentratek-id/alpine-php/fpm:8.3

LABEL org.opencontainers.image.source=https://github.com/sentratek-id/alpine-php

RUN apk update \
    && apk add nginx supervisor \
    && apk cache clean

RUN mkdir -p /var/www/public
RUN mkdir -p /etc/nginx/conf.d

COPY nginx.conf /etc/nginx/

COPY site.conf /etc/nginx/conf.d/

COPY supervisor /etc/supervisor

COPY index.php /var/www/public

WORKDIR /var/www

# Add a non-root user to prevent files being created with root permissions on host machine.
ONBUILD ARG USER_CONTAINER=worker
ONBUILD ENV USER_CONTAINER=${USER_CONTAINER}

ONBUILD RUN adduser -D ${USER_CONTAINER} ${USER_CONTAINER}

EXPOSE 80

STOPSIGNAL SIGTERM

CMD ["/usr/bin/supervisord", "-n", "-c", "/etc/supervisor/supervisord.conf"]

