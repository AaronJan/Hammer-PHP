# ==============================================================================
# Hammer PHP
# PHP docker image that makes your development easier.
# 
# @license Apache-2.0
# ==============================================================================

FROM php:7.1-fpm
MAINTAINER AaronJan <aaronjan@qq.com>

RUN apt-get update \
# install necessary dependencies.
        && apt-get install rsync zip unzip openssh-client libxml2-dev libfreetype6-dev libjpeg62-turbo-dev libmcrypt-dev libpng12-dev libssl-dev libgmp-dev wget -y \
# install common extensions.
        && docker-php-ext-install -j$(nproc) iconv zip soap pdo_mysql bcmath gmp \
# GD extension must be configured manually.
        && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
        && docker-php-ext-install -j$(nproc) gd \
# these extension must be installed with PECL.
        && pecl install mongodb ast \
        && docker-php-ext-enable mongodb ast

# Supervisor providers extensibility, Cron lets you do task scheduling.
RUN apt-get install supervisor vim cron git -y

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
# /root/.ssh/ for SSH stuff.
        && mkdir -p ~/.ssh \

# copy configuration.
COPY conf/supervisor.conf /web/configs/supervisord.conf

# install Composer.
WORKDIR /web/temp
COPY scripts/install_composer.sh /web/scripts/install_composer.sh
RUN sh /web/scripts/install_composer.sh

# start service.
RUN /etc/init.d/cron start

# switch to your project folder.
WORKDIR /web/application

# start application using Supervisord.
CMD ["/usr/bin/supervisord", "-c", "/web/configs/supervisord.conf"]