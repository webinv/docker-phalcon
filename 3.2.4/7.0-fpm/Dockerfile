FROM php:7.0-fpm

# Maintainer
LABEL maintainer "krzysztof@kardasz.eu"

# Update system and install required packages
ENV DEBIAN_FRONTEND noninteractive

# Common tools
RUN \
    apt-get -y update && \
    apt-get -y install curl git telnet vim autoconf file g++ gcc libc-dev make pkg-config re2c wget ca-certificates supervisor apt-transport-https software-properties-common

# NodeJS and frontend utils
RUN \
    curl -sL https://deb.nodesource.com/setup_8.x | bash - && \
    apt-get install -y nodejs; \
    npm install -g gulp bower;

# PHP extensions
RUN apt-get update && apt-get install -y --no-install-recommends \
        libpq-dev \
        libbz2-dev \
        libzip-dev \
        libcurl4-openssl-dev \
        libicu-dev \
        libxslt-dev \
        libtidy-dev \
        libxml2-dev \
        libmcrypt-dev \
        libmemcached-dev \
        libssl-dev \
        libsqlite3-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng12-dev \
        libwebp-dev \
        librabbitmq-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install bcmath \
    && docker-php-ext-install bz2 \
    && docker-php-ext-install ctype \
    && docker-php-ext-install curl \
    && docker-php-ext-install dom \
    && docker-php-ext-install exif \
    && docker-php-ext-install fileinfo \
    && docker-php-ext-install gettext \
    && docker-php-ext-install hash \
    && docker-php-ext-install iconv \
    && docker-php-ext-install intl \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install mcrypt \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install opcache \
    && docker-php-ext-install pcntl \
    && docker-php-ext-install pgsql \
    && docker-php-ext-install phar \
    && docker-php-ext-install pdo \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install pdo_pgsql \
    && docker-php-ext-install pdo_sqlite \
    && docker-php-ext-install soap \
    && docker-php-ext-install sockets \
    && docker-php-ext-install simplexml \
    && docker-php-ext-install tidy \
    && docker-php-ext-install xml \
    && CFLAGS="-I/usr/src/php" docker-php-ext-install xmlreader \
    && docker-php-ext-install xmlrpc \
    && docker-php-ext-install xmlwriter \
    && docker-php-ext-install xsl \
    && rm -rf /var/lib/apt/lists/*

# PECL extensions
RUN \
    pecl install zip-1.13.5 && \
    docker-php-ext-enable zip && \
    pecl install mongodb && \
    docker-php-ext-enable mongodb

# PHP Tools
RUN \
    curl -sS https://getcomposer.org/installer | /usr/local/bin/php -- --install-dir=/usr/local/bin --filename=composer && \
    wget -O /usr/local/bin/apigen http://apigen.org/apigen.phar && chmod +x /usr/local/bin/apigen && \
    wget -O /usr/local/bin/phpdoc http://phpdoc.org/phpDocumentor.phar && chmod +x /usr/local/bin/phpdoc && \
    wget -O /usr/local/bin/phpunit https://phar.phpunit.de/phpunit.phar && chmod +x /usr/local/bin/phpunit && \
    curl -LsS http://symfony.com/installer > /usr/local/bin/symfony && chmod a+x /usr/local/bin/symfony && \
    wget -O /usr/local/bin/wp https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar && chmod +x /usr/local/bin/wp
    
# Phalcon v 3.2.4
RUN \
    curl -O https://codeload.github.com/phalcon/cphalcon/tar.gz/v3.2.4 && \
    tar xvzf v3.2.4 && \
    cd cphalcon-3.2.4/build && ./install && \
    cd ../../ && rm -Rf cphalcon-3.2.4 && rm -Rf v3.2.4 && \
    echo "extension=phalcon.so" > /usr/local/etc/php/conf.d/phalcon.ini

# add configs & data after package install (so packages won't override them)
ADD ./usr /usr

COPY docker-entrypoint.sh /entrypoint.sh

RUN chmod a+x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 9000

CMD ["/usr/local/sbin/php-fpm", "--fpm-config", "/usr/local/etc/php-fpm.conf", "-F", "-c", "/usr/local/etc/php/php.ini"]