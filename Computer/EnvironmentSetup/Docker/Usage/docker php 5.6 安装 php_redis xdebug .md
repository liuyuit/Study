#  docker php 5.6 安装 php_redis xdebug 

## references

> https://blog.csdn.net/weixin_33982670/article/details/92102677
>
> https://segmentfault.com/a/1190000010833434

## 创建一个网络

```
RUN mkdir -p /usr/src/php/ext/redis \
    && curl -L https://github.com/phpredis/phpredis/archive/3.1.6.tar.gz | tar xvz -C /usr/src/php/ext/redis --strip 1 \
    && echo 'redis' >> /usr/src/php-available-exts \
    && docker-php-ext-install redis
```



Dockerfile

```
# Set the base image for subsequent instructions
FROM php:5.6-fpm

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Update packages
RUN sed -i "s@http://deb.debian.org@http://mirrors.aliyun.com@g" /etc/apt/sources.list \
    && rm -Rf /var/lib/apt/lists/* \
    && apt-get update \
    && curl -sL https://deb.nodesource.com/setup_14.x | bash - \
    && apt-get install -y nodejs netcat libmcrypt-dev libjpeg-dev libpng-dev libfreetype6-dev libbz2-dev libonig-dev \
     libzip-dev zlibc git vim procps supervisor \
    && apt-get clean

# Install extensions
RUN docker-php-ext-install pdo_mysql mysqli mysql mbstring zip exif pcntl \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/freetype2 --with-png-dir=/usr --enable-gd-native-ttf --with-jpeg-dir=/usr \
    && docker-php-ext-install gd \
    && docker-php-ext-install -j$(nproc) bcmath \
	&& mkdir -p /usr/src/php/ext/redis \
    && curl -L https://github.com/phpredis/phpredis/archive/3.1.6.tar.gz | tar xvz -C /usr/src/php/ext/redis --strip 1 \
    && echo 'redis' >> /usr/src/php-available-exts \
    && pecl install -o -f xdebug \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable xdebug


COPY . .

CMD  php-fpm \
    && supervisord -c  /etc/supervisor/supervisord.conf

```