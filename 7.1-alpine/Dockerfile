# ==============================================================================
# Hammer PHP
# Docker PHP image on steroids.
# 
# @license Apache-2.0
# ==============================================================================

FROM php:7.1-fpm-alpine
MAINTAINER AaronJan <aaronjan@qq.com>

# install necessary dependencies.
RUN apk add --no-cache rsync zip unzip openssh-client libxml2-dev freetype-dev libjpeg-turbo-dev libmcrypt-dev libpng-dev openssl-dev gmp-dev autoconf gcc clang g++ make wget \
# install common extensions.
        && NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
        && docker-php-ext-install -j${NPROC} iconv zip soap pdo_mysql bcmath gmp \
# GD extension must be configured manually.
        && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
        && docker-php-ext-install -j${NPROC} gd \
# these extension must be installed with PECL.
        && pecl install mongodb ast \
        && docker-php-ext-enable mongodb ast

# Supervisor providers extensibility, Cron lets you do task scheduling.
RUN apk add --no-cache supervisor vim git

# create basic folders.
#
# /web/application/ is for your project.
RUN mkdir -p /web/application/ \
# /web/config/ for all your configurations.
        && mkdir -p /web/configs/ \
# /web/scripts/ for executable scripts.
        && mkdir -p /web/scripts/ \
# /web/temp/ for temporary files.
        && mkdir -p /web/temp/ \
# /web/crontabs/ for Cron.
        && mkdir -p /web/crontabs/ \
# /root/.ssh/ for SSH stuff.
        && mkdir -p ~/.ssh \

# copy configuration.
COPY conf/supervisor.conf /web/configs/supervisord.conf

# install Composer.
WORKDIR /web/temp
COPY scripts/install_composer.sh /web/scripts/install_composer.sh
RUN sh /web/scripts/install_composer.sh \
        && mv ./composer.phar /usr/local/bin/composer \
        && chmod +x /usr/local/bin/composer

# switch to your project folder.
WORKDIR /web/application

# start application using Supervisord.
CMD ["/usr/bin/supervisord", "-c", "/web/configs/supervisord.conf"]