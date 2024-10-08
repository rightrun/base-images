############################################
# PHPDocker.io PHP 8.1 / CLI and FPM image #
############################################

FROM ubuntu:jammy AS cli

# Fixes some weird terminal issues such as broken clear / CTRL+L
ENV TERM=linux

# Ensure apt doesn't ask questions when installing stuff
ENV DEBIAN_FRONTEND=noninteractive

# Install Ondrej repos for Ubuntu jammy, PHP, composer and selected extensions - better selection than
# the distro's packages
RUN apt-get update \
    && apt-get install -y --no-install-recommends gnupg \
    && echo "deb http://ppa.launchpad.net/ondrej/php/ubuntu jammy main" > /etc/apt/sources.list.d/ondrej-php.list \
    && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4F4EA0AAE5267A6C \
    && apt-get update \
    && apt-get -y --no-install-recommends install \
        ca-certificates \
        curl \
        vim \
        unzip \
        php8.1-apcu \
        php8.1-cli \
        php8.1-curl \
        php8.1-mbstring \
        php8.1-opcache \
        php8.1-readline \
        php8.1-xml \
        php8.1-redis \
        php8.1-zip \
        php8.1-bcmath \
        php8.1-mcrypt \
        php8.1-gd \
        php8.1-gnupg \
        php8.1-imagick \
        php8.1-mysql \
        php8.1-rdkafka \
        php8.1-yaml \
        php8.1-tideways \
        php8.1-sqlite3 \
        php8.1-tidy \
        php8.1-bz2 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* ~/.composer

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

CMD ["php", "-a"]

# If you'd like to be able to use this container on a docker-compose environment as a quiescent PHP CLI container
# you can /bin/bash into, override CMD with the following - bear in mind that this will make docker-compose stop
# slow on such a container, docker-compose kill might do if you're in a hurry
# CMD ["tail", "-f", "/dev/null"]

FROM cli AS fpm

# Install FPM
RUN apt-get update \
    && apt-get -y --no-install-recommends install php8.1-fpm \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

STOPSIGNAL SIGQUIT

# PHP-FPM packages need a nudge to make them docker-friendly
COPY overrides.conf /etc/php/8.1/fpm/pool.d/z-overrides.conf

CMD ["/usr/sbin/php-fpm8.1", "-O" ]

# Open up fcgi port
EXPOSE 9000


FROM cli AS swoole

# Install the latest available published swoole release
RUN apt-get update \
    && apt-get -y --no-install-recommends install php8.1-swoole \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*
