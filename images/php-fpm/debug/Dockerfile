ARG PHP_VERSION=
FROM docker.io/smartbeetles/images/php-fpm:${PHP_VERSION}
USER root

RUN set -eux \
    && yum install -y php-pecl-xdebug \
    && yum clean all

COPY debug/etc/*.ini /etc/
COPY debug/etc/php.d/*.ini /etc/php.d/

USER www-data
