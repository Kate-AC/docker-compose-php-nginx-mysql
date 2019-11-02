FROM php:7.1-fpm-alpine
MAINTAINER Kate-AC

RUN apk update
RUN apk upgrade
RUN apk del grep
RUN apk add --no-cache \
  automake \
  libxml2-dev \
  libtool \
  python-dev \
  musl-dev \
  libffi-dev \
  nodejs \
  nodejs-dev \
  nodejs-npm \
  curl-dev \
  bzip2-dev \
  openssl-dev \
  openssh \
  ssmtp \
  bash \
  git \
  openssl \
  openssl-dev \
  readline \
  autoconf \
  automake \
  curl \
  tar \
  zip \
  mysql-client \
  ca-certificates \
  make \
  gcc \
  g++ \
  wget \
  rsync \
  freetype \
  libpng \
  libpng-dev \
  libjpeg-turbo \
  freetype-dev \
  libpng-dev \
  libjpeg-turbo-dev

RUN rm -rf /var/cache/apk/*

RUN mkdir -p /var/run/mysqld
RUN mkdir -p /project/Hoge

RUN apk add --update --no-cache --virtual=.build-dependencies py-pip \
  && pip install --upgrade pip

RUN pecl channel-update pecl.php.net

###### gRPCを利用する ######

RUN pecl install grpc

RUN pecl install protobuf

RUN cd /usr/src \
  && wget https://github.com/protocolbuffers/protobuf/releases/download/v3.6.1/protoc-3.6.1-linux-x86_64.zip \
  && unzip protoc-3.6.1-linux-x86_64.zip -d protoc \
  && ln -s /usr/src/grpc/third_party/protobuf/src/protoc /usr/bin/protoc

RUN cd /usr/src \
  && git clone -b $(curl -L https://grpc.io/release) https://github.com/grpc/grpc \
  && cd grpc \
  && git submodule update --init \
  && make grpc_php_plugin \
  && ln -s /usr/src/grpc/bins/opt/grpc_php_plugin /usr/bin/grpc_php_plugin

############################

RUN docker-php-ext-install pdo_mysql mysqli

RUN docker-php-ext-install soap

RUN docker-php-ext-install xmlrpc

RUN docker-php-ext-install opcache

RUN docker-php-ext-install bcmath

RUN docker-php-ext-configure gd \
  --with-freetype-dir=/usr/lib/ \
  --with-png-dir=/usr/lib/ \
  --with-jpeg-dir=/usr/lib/ \
  --with-gd

RUN NUMPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
    && docker-php-ext-install -j${NUMPROC} gd

RUN docker-php-source extract \
    && apk add --no-cache --virtual .phpize-deps-configure $PHPIZE_DEPS \
    && pecl install apcu \
    && docker-php-ext-enable apcu \
    && apk del .phpize-deps-configure \
    && docker-php-source delete

## set php
COPY ./docker/config/php/errors.ini /usr/local/etc/php/conf.d
COPY ./docker/config/php/mbstring.ini /usr/local/etc/php/conf.d
COPY ./docker/config/php/memory_limit.ini /usr/local/etc/php/conf.d
COPY ./docker/config/php/output_buffer.ini /usr/local/etc/php/conf.d
COPY ./docker/config/php/timezone.ini /usr/local/etc/php/conf.d
COPY ./docker/config/php/extension.ini /usr/local/etc/php/conf.d

 ## php-fpm
COPY ./docker/config/php-fpm/zzz-socket.conf /usr/local/etc/php-fpm.d
COPY ./docker/config/php-fpm/zzz-status.conf /usr/local/etc/php-fpm.d
COPY ./docker/config/php-fpm/zzz-www.conf /usr/local/etc/php-fpm.d

RUN cd /usr/local/bin \
 && curl -sS https://getcomposer.org/installer | php \
 && mv /usr/local/bin/composer.phar /usr/local/bin/composer

COPY ./composer.json /root/.composer

WORKDIR /project/Hoge

ENTRYPOINT ["/usr/local/sbin/php-fpm"]
