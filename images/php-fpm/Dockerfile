ARG PHP_VERSION=7.0
ARG PHP_VARIANT="fpm-loaders"
FROM smartbeetles/php:${PHP_VERSION}-${PHP_VARIANT}

# Clear undesired settings from base fpm images
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV COMPOSER_HOME=/tmp

ENV MAILHOG_HOST    mailhog
ENV MAILHOG_PORT    1025

RUN yum install -y which pv sudo bind-utils python3-pip bash-completion rsync socat oathtool \
        wget ack make gcc gcc-c++ automake autoconf openvpn crontabs mariadb \
    && yum clean all \
    && rm -rf /var/cache/yum

ENV NODE_VERSION    10
RUN npm install -g n \
    && n install ${NODE_VERSION} \
    && rm -rf /usr/local/n/versions/node

COPY docker-entrypoint /usr/local/bin/
COPY etc/profile.d/*.sh /etc/profile.d/
COPY etc/php.d/*.ini /etc/php.d/
COPY etc/php.d/05-additions.ini.template /etc/php.d/

# Install mhsendmail to support routing email through mailhog
RUN mkdir -p /tmp/mhsendmail \
    && cd /tmp/mhsendmail \
    && curl -vLOs https://github.com/mailhog/mhsendmail/releases/download/v0.2.0/mhsendmail_linux_amd64 \
    && chmod +x mhsendmail_linux_amd64 \
    && mv mhsendmail_linux_amd64 /usr/local/bin/mhsendmail

# Configure www-data user as primary php-fpm user for better local dev experience
RUN useradd www-data \
    && chmod 0755 ~www-data \
    && mkdir -p /var/www/html \
    && chown www-data:www-data /var/www/html \
    && echo "www-data ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/nopasswd \
    && PHP_FPM_USER=$(grep -i '^user = ' /etc/php-fpm.d/www.conf | awk '{print $3}') \
    && find /var/log /var/lib/php -uid $(id -u ${PHP_FPM_USER}) | xargs chown www-data \
    && perl -pi -e 's/^(user|group) = php-fpm$/$1 = www-data/g' /etc/php-fpm.d/www.conf

WORKDIR /var/www/html
USER www-data
