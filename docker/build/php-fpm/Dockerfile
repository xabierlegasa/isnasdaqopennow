FROM php:7.3-fpm

######
# You can install php extensions using docker-php-ext-install
######

RUN apt-get update && apt-get install -y \
    libc-client-dev \
    libkrb5-dev \
    libpng-dev \
    vim \
    git \
    zip \
    libzip-dev \
    zlib1g-dev \
    libicu-dev \
    g++ \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    libgmp-dev \
    ffmpeg \
    gnupg \
    procps \
    libxml2-dev \
    && rm -r /var/lib/apt/lists/*

RUN apt-key adv --recv-keys --keyserver keyserver.ubuntu.com EB563F93142986CE
RUN apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 3B4FE6ACC0B21F32

RUN printf "deb http://es.archive.ubuntu.com/ubuntu/ bionic main restricted\ndeb http://es.archive.ubuntu.com/ubuntu/ bionic-updates main restricted\ndeb http://es.archive.ubuntu.com/ubuntu/ bionic main universe\ndeb http://es.archive.ubuntu.com/ubuntu/ bionic-updates main universe\ndeb http://es.archive.ubuntu.com/ubuntu/ bionic multiverse\ndeb http://es.archive.ubuntu.com/ubuntu/ bionic-updates multiverse" > /etc/apt/sources.list

RUN apt-get update && apt-get install -y \
    tesseract-ocr \
    libtesseract-dev \
    && rm -r /var/lib/apt/lists/*

RUN ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h

RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-configure intl \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install imap \
        pdo \
        pdo_mysql \
        bcmath \
        gd \
        intl \
        gmp \
        zip \
        sockets \
        soap

RUN curl --silent --show-error https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer global require "hirak/prestissimo"
RUN composer --version

# Install xdebug
RUN pecl install xdebug
# Configure php and xdebug
ADD php.ini /usr/local/etc/php/

ENV XDEBUGINI_PATH=/usr/local/etc/php/conf.d/xdebug.ini
RUN echo "zend_extension="`find /usr/local/lib/php/extensions/ -iname 'xdebug.so'` > $XDEBUGINI_PATH
COPY xdebug.ini /tmp/xdebug.ini
RUN cat /tmp/xdebug.ini >> $XDEBUGINI_PATH
RUN echo "xdebug.remote_host="`/sbin/ip route|awk '/default/ { print $3 }'` >> $XDEBUGINI_PATH


## Supervisor
#RUN apt-get update && apt-get install -y supervisor
#
#CMD ["/usr/bin/supervisord"]
