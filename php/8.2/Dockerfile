############################################
# PHPDocker.io PHP 8.2 / CLI, FPM and Swoole image #
############################################

### CLI ###

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
        vim \
        curl \
        unzip \
        php8.2-apcu \
        php8.2-cli \
        php8.2-curl \
        php8.2-mbstring \
        php8.2-opcache \
        php8.2-readline \
        php8.2-xml \
        php8.2-zip \
        php8.2-redis \
        php8.2-bcmath \
        php8.2-mcrypt \
        php8.2-gd \
        php8.2-gnupg \
        php8.2-imagick \
        php8.2-mysql \
        php8.2-rdkafka \
        php8.2-yaml \
        php8.2-tideways \
        php8.2-sqlite3 \
        php8.2-tidy \
        php8.2-bz2 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* ~/.composer

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

CMD ["php", "-a"]

### FPM ###

FROM cli AS fpm

# Install FPM
RUN apt-get update \
    && apt-get -y --no-install-recommends install php8.2-fpm \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

STOPSIGNAL SIGQUIT

# PHP-FPM packages need a nudge to make them docker-friendly
COPY overrides.conf /etc/php/8.2/fpm/pool.d/z-overrides.conf

CMD ["/usr/sbin/php-fpm8.2", "-O" ]

# Open up fcgi port
EXPOSE 9000


FROM cli AS swoole

# Install the latest available published swoole release
RUN apt-get update \
    && apt-get -y --no-install-recommends install php8.2-swoole \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*