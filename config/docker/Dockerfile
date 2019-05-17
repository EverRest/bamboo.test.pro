FROM debian:jessie
MAINTAINER Pavlo Medynskyy <medynskyypavlo@gmail.com>
# https://github.com/websupport-sk/pecl-memcache

RUN \
    apt-get update && \
    apt-get install -y --no-install-recommends curl wget apt-transport-https lsb-release ca-certificates apt-utils && \
    wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg && \
    echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list \
    && apt-get update \
    && apt-get install -y php7.2-dev \
    && apt-get install -y apache2 libapache2-mod-php7.2 lynx-cur zlib1g-dev \
    && apt-get install -y --no-install-recommends \
        git \
        wget \
        vim \
	    zip \
#	    memcached\
        unzip \
        gettext \
        php7.2-cli \
        php7.2-curl \
        php7.2-gd \
        php7.2-imagick \
        php7.2-imap \
#        php7.2-mcrypt \
        php7.2-xmlrpc \
        php7.2-mysqlnd \
        php7.2-mbstring \
        php7.2-intl \
        php7.2-xml \
        php7.2-imagick \
        php7.2-xml \
        php7.2-yaml \
        php7.2-soap \
#        php5.6-memcached \
        libyaml-dev \
#        php-pear \
        imagemagick \
        wkhtmltopdf \
        xvfb \
        xauth \
        php-xdebug \
# This is for php7.2
#    && wget https://github.com/websupport-sk/pecl-memcache/ \
#    && unzip NON_BLOCKING_IO_php7.zip \
#    && cd php-memcached \
#    && phpize \
#    && ./configure \
#    && make \
#    && make install \
#    && echo 'extension=memcache.so' > /etc/php/7.2/cli/conf.d/memcache.ini \
#    && pecl install yaml-1.3.0 \
#    && echo 'extension=yaml.so' | tee /etc/php/7.2/mods-available/yaml.ini \
    && apt-get purge -y \
        wget \
        php7.2-dev \
    && apt-get autoremove -y \
    && apt-get clean -y \
    && apt-get purge -y \
    && rm -rf \
        /var/lib/dpkg/ \
        /var/lib/cache/man/* \
        /usr/share/doc/* \
        /usr/share/man/* \
        /usr/share/locale/* \
        /var/lib/apt/lists/* \
        /tmp/* \
        /var/tmp/* \
#        /NON_BLOCKING_IO_php7.zip \
#        /pecl-memcache-NON_BLOCKING_IO_php7 \
    && mkdir -p /run/php

# Update the PHP.ini file, enable <? ?> tags and quieten logging.
RUN sed -i "s/short_open_tag = Off/short_open_tag = On/" /etc/php/7.2/apache2/php.ini
RUN sed -i "s/error_reporting = .*$/error_reporting = E_ERROR | E_WARNING | E_PARSE/" /etc/php/7.2/apache2/php.ini

# Manually set up the apache environment variables
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid

RUN  curl -o /tmp/composer-setup.php https://getcomposer.org/installer \
    && php /tmp/composer-setup.php --install-dir=bin --filename=composer \
    && rm /tmp/composer-setup.php

# Enable apache mods.
RUN a2enmod php7.2
RUN a2enmod rewrite
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf


#COPY php.ini /etc/php/5.6/fpm/php.ini
#COPY www.conf /etc/php/5.6/fpm/pool.d/www.conf
COPY php_cli.ini /etc/php/7.2/cli/php.ini
COPY php.ini /etc/php/7.2/apache2/php.ini
#COPY opcache.ini /etc/php/5.6/mods-available/opcache.ini
CMD cd /var/www
CMD cp .env.example .env
CMD php composer install
CMD php artisan migrate

EXPOSE 80
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]