###########################################
# PHPDocker.io PHP 7.2 / CLI & FPM images & swoole #
###########################################

FROM ubuntu:focal AS cli

# Fixes some weird terminal issues such as broken clear / CTRL+L
ENV TERM=linux

# Ensure apt doesn't ask questions when installing stuff
ENV DEBIAN_FRONTEND=noninteractive

# Install Ondrej repos for Ubuntu focal, PHP7.2, composer and selected extensions - better selection than
# the distro's packages
RUN apt-get update \
    && apt-get install -y --no-install-recommends gnupg \
    && echo "deb http://ppa.launchpad.net/ondrej/php/ubuntu focal main" > /etc/apt/sources.list.d/ondrej-php.list \
    && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4F4EA0AAE5267A6C \
    && apt-get update \
    && apt-get -y --no-install-recommends install \
        ca-certificates \
        curl \
        unzip \
        php7.2-common \
        php7.2-apcu \
        php7.2-bcmath \
        php7.2-tideways \
        php7.2-uuid \
        php7.2-sqlite3 \
        php7.2-rdkafka \
        php7.2-protobuf \
        php7.2-yaml \
        php7.2-apcu-bc \
        php7.2-cli \
        php7.2-curl \
        php7.2-gd \
        php7.2-imagick \
        php7.2-json \
        php7.2-mbstring \
        php7.2-opcache \
        php7.2-mcrypt \
        php7.2-igbinary \
        php7.2-readline \
        php7.2-xml \
        php7.2-zip \
        php7.2-bz2 \
        php7.2-redis \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

CMD ["php", "-a"]

# If you'd like to be able to use this container on a docker-compose environment as a quiescent PHP CLI container
# you can /bin/bash into, override CMD with the following - bear in mind that this will make docker-compose stop
# slow on such a container, docker-compose kill might do if you're in a hurry
# CMD ["tail", "-f", "/dev/null"]

FROM cli AS fpm

# Install FPM
RUN apt-get update \
    && apt-get -y --no-install-recommends install php7.2-fpm \
    && pecl install yaf-3.0.7 \
    && docker-php-ext-enable yaf \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

STOPSIGNAL SIGQUIT

# PHP-FPM packages need a nudge to make them docker-friendly
COPY overrides.conf /etc/php/7.2/fpm/pool.d/z-overrides.conf

CMD ["/usr/sbin/php-fpm7.2", "-O" ]

# Open up fcgi port
EXPOSE 9000

FROM cli AS swoole

# Install the latest available published swoole release
RUN apt-get update \
    && apt-get -y --no-install-recommends install php7.2-swoole \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*
