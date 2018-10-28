---
title: Cài đặt môi trường cho laravel dùng docker
date: 2018-10-28 09:25:45
tags: 
- docker
- TIL
---

## 1. Cài đặt laravel

```
composer create-project --prefer-dist laravel/laravel blog
```

## 2. Tạo file docker-compose.yml
Tạo file docker-compose.yml trong thư mục của laravel.

```
version: '2'
services:

  # The Application
  app:
    build:
      context: ./
      dockerfile: app.dockerfile
    working_dir: /var/www
    volumes:
      - ./:/var/www
    environment:
      - "DB_PORT=3306"
      - "DB_HOST=database"

  # The Web Server
  web:
    build:
      context: ./
      dockerfile: web.dockerfile
    working_dir: /var/www
    volumes_from:
      - app
    ports:
      - 8080:80

  # The Database
  database:
    image: mysql
    volumes:
      - dbdata:/var/lib/mysql
    environment:
      - "MYSQL_DATABASE=homestead"
      - "MYSQL_USER=homestead"
      - "MYSQL_PASSWORD=secret"
      - "MYSQL_ROOT_PASSWORD=secret"
    ports:
        - "33061:3306"

volumes:
  dbdata:
```
Tạo file app.dockerfile

```
FROM php:7-fpm

RUN apt-get update && apt-get install -y libmcrypt-dev \
    mysql-client libmagickwand-dev --no-install-recommends \
    && docker-php-ext-install mcrypt pdo_mysql
```

Tạo file web.dockerfile

```
FROM nginx

ADD vhost.conf /etc/nginx/conf.d/default.conf
```

Tạo file vhost.conf

```
server {
    listen 80;
    index index.php index.html;
    root /var/www/public;

    location / {
        try_files $uri /index.php?$args;
    }

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}
```

## 3. Chạy docker

Chạy lệnh này để cài đặt tất cả các dịch vụ 
```
docker-compose up
```

Chạy các lệnh artisan hay một số lệnh bên trong docker dùng

```
docker-compose exec app "__ Lệnh muốn chạy __"

Ví dụ:

docker-compose exec app php artisan migrate
```