FROM php:7.2-fpm
LABEL maintainer="1605882811@qq.com"


# set timezome
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#安装加速
#RUN cp /etc/apt/sources.list  /etc/apt/sources.list.bak
#ADD ./sources.list /etc/apt/

# Install package and PHP Core extensions
RUN apt-get update

RUN apt-get install -y \
        curl \
        wget \
        git \
        zip \
        libz-dev \
        libssl-dev \
        libnghttp2-dev \
        libpcre3-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libevent-dev

RUN	docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
#    && --with-memcached=/home/app/memcached/bin/memcached \
	&& docker-php-ext-install -j$(nproc) gd \
        && docker-php-ext-install zip \
        && docker-php-ext-install pdo_mysql \
        && docker-php-ext-install opcache \
        && docker-php-ext-install mysqli \
        && docker-php-ext-install bcmath \
        && docker-php-ext-install sockets \
        && rm -r /var/lib/apt/lists/*

# Copy extensions had downloaded
COPY ./pkg/redis.tgz /home/redis.tgz
#COPY swoole
COPY ./pkg/swoole.tar.gz /home/swoole.tar.gz
#COPY memcached
COPY ./pkg/php-memcached-php7.tar.gz /home/memcached.tar.gz
COPY ./pkg/libmemcached-0.44.tar.gz /home/libmemcached-0.44tar.gz
COPY ./pkg/libmemcached-1.0.18.tar.gz /home/libmemcached-1.0.18.tar.gz

# Install PECL extensions (Redis)
RUN pecl install /home/redis.tgz && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini

# Install the php memcached extension
RUN cd /home \
    && tar xvzf libmemcached-1.0.18.tar.gz \
    && cd libmemcached-1.0.18 \
    && ./configure --with-memcached \
    && make \
    && make install \
    && rm /home/libmemcached-1.0.18.tar.gz

RUN cd /home \
    && mkdir -p memcached \
    && tar -C memcached -zxvf /home/memcached.tar.gz --strip 1 \
    && ( \
            cd memcached \
            && phpize \
            && ./configure --disable-memcached-sasl\
            && make -j$(nproc) \
            && make install \
        ) \
#        && rm -r /home/memcached \
        && rm /home/memcached.tar.gz

RUN docker-php-ext-enable memcached

# Install Phalcon extensions
#RUN cd /home \
#    && tar -zxvf cphalcon.tar.gz \
#    && mv cphalcon-* phalcon \
#    && cd phalcon/build \
#    && ./install \
#    && echo "extension=phalcon.so" > /usr/local/etc/php/conf.d/phalcon.ini

# Swoole extension
RUN cd /home \
    && mkdir -p swoole \
    && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
    && ( \
        cd swoole \
        && phpize \
        && ./configure --enable-async-redis --enable-mysqlnd --enable-openssl --enable-http2 \
        && make -j$(nproc) \
        && make install \
    ) \
    && rm -r /home/swoole \
    && docker-php-ext-enable swoole

# Install Composer

#composer安装文件
#COPY ./pkg/installer /home/installer
COPY ./pkg/composer.phar /home/composer.phar
ENV COMPOSER_HOME /root/composer
#RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
#RUN wget https://getcomposer.org/installer \
#    && php installer \
#    && mv composer.phar /usr/local/bin/composer
#RUN php /home/installer \
#    && mv composer.phar /usr/local/bin/composer
RUN mv /home/composer.phar /usr/local/bin/composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH

RUN rm -f /home/redis.tgz \
#        rm -f /home/cphalcon.tar.gz \
        rm -f /home/swoole.tar.gz \
        rm -f /home/composer.phar

WORKDIR /data

#composer切换国内源
RUN composer config -g repo.packagist composer https://packagist.phpcomposer.com

# Write Permission
RUN usermod -u 1000 www-data
