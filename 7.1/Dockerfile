FROM php:7.1-fpm
MAINTAINER AaronJan <aaronjan@qq.com>

RUN apt-get update \
        && apt-get install rsync zip unzip openssh-client libfreetype6-dev libjpeg62-turbo-dev libmcrypt-dev libpng12-dev libssl-dev openssl-dev -y \
        && pecl install mongodb \
        && docker-php-ext-enable mongodb \
        && docker-php-ext-install -j$(nproc) iconv pdo_mysql \
        && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
        && docker-php-ext-install -j$(nproc) gd

RUN apt-get install supervisor vim cron -y

RUN mkdir -p /web/application/ \
		&& mkdir -p /web/config/ \
        && mkdir -p /web/tools/ \
        && mkdir -p ~/.ssh \
		&& /etc/init.d/cron start

COPY config/supervisor.conf /web/config/supervisord.conf
COPY lib/composer-1.4.1.phar /usr/bin/composer

RUN chmod +x /usr/bin/composer

WORKDIR /web/application

CMD ["/usr/bin/supervisord", "-c", "/web/config/supervisord.conf"]