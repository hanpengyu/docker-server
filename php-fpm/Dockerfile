FROM php:7.4-fpm

LABEL maintainer="HanPengYu <han.pengyu@foxmail.com>"

# 修改源
RUN /bin/sh -c set -eux; \
    sed -i 's/deb.debian.org/mirrors.tuna.tsinghua.edu.cn/' /etc/apt/sources.list && \
    sed -i 's/security.debian.org/mirrors.tuna.tsinghua.edu.cn/' /etc/apt/sources.list && \
    sed -i 's/security-cdn.debian.org/mirrors.tuna.tsinghua.edu.cn/' /etc/apt/sources.list

RUN /bin/sh -c set -xe && apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y --no-install-recommends curl  \
        libmemcached-dev libz-dev libpq-dev libjpeg-dev libpng-dev libfreetype6-dev libssl-dev libmcrypt-dev

RUN /bin/sh -c set -xe \
    && docker-php-ext-install pdo_mysql  \
    && docker-php-ext-install pdo_pgsql  \
    && docker-php-ext-configure gd --prefix=/usr --with-jpeg --with-freetype  \
    && docker-php-ext-install gd \
    && docker-php-ext-install bcmath \
    && docker-php-ext-install exif \
    && docker-php-ext-install gettext \
    && docker-php-ext-install pcntl \
    && docker-php-ext-install mysqli \

# always run apt update when start and after add new source list, then clean up at end.
RUN set -xe; \
    apt-get update -yqq  \
    && pecl channel-update pecl.php.net  \
    && apt-get install -yqq apt-utils gnupg2 git libzip-dev zip unzip  \
    && docker-php-ext-configure zip && docker-php-ext-install zip \
    && apt-get -yqq install libbz2-dev && docker-php-ext-install bz2

RUN pecl install -o -f mcrypt \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable mcrypt

USER root

# redis扩展
RUN pecl install -o -f redis \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis

# memcache扩展
RUN echo '' | pecl -q install memcached \
    && docker-php-ext-enable memcached

RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000