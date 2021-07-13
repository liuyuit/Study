#  MacOs中的Docker非常慢

## references

> https://mlog.club/article/165772
>
> https://learnku.com/laravel/t/37221
>

```
volumes:
        - ./:/var/www:cached
        - ${DOCKER_PHP_LOG_DIR}:/var/log/php:cached
```

docker-compose.yml

```
  version: "3"

  services:
    php:
      build:
        context: .
      container_name: material_php
      working_dir: /var/www
      volumes:
        - ./:/var/www:cached
        - ${DOCKER_PHP_LOG_DIR}:/var/log/php:cached
        - ${DOCKER_PHP_CONFIG_DIR}:/usr/local/etc/php:cached
      ports:
        - "${DOCKER_PHP_SERVE_PORT}:8000"
        - "${DOCKER_PHP_PORT}:9000"
      stdin_open: true
      tty: true
      environment:
        - TZ=Asia/Shanghai

      depends_on:
        - mysql
        - redis

    nginx:
      container_name: material_nginx
      build:
        context: ./ini/docker/nginx
      volumes:
        - ${DOCKER_NGINX_CONFIG_DIR}:/etc/nginx:cached
        - ${DOCKER_NGINX_LOG_DIR}:/var/log/nginx:cached
        - ./:/var/www
      ports:
        - "${DOCKER_NGINX_PORT}:80"
      environment:
        - TZ=Asia/Shanghai
      depends_on:
        - php

    redis:
      build:
        context: ./ini/docker/redis
      container_name: material_redis
      ports:
        - "${DOCKER_REDIS_PORT}:6379"
      volumes:
        - ${DOCKER_REDIS_CONFIG_DIR}:/usr/local/etc/redis:cached

    mysql:
      image: mysql:5.7
      container_name: material_mysql
      ports:
        - "${DOCKER_MYSQL_PORT}:3306"
      volumes:
        - material_mysql_data:/var/lib/mysql:cached
      environment:
        MYSQL_DATABASE: laravue
        MYSQL_USER: laravue
        MYSQL_PASSWORD: laravue
        MYSQL_ROOT_PASSWORD: root

  volumes:
    material_mysql_data:
      external: false
```

