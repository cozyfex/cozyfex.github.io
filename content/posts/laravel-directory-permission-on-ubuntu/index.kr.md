---
title: "Ubuntu 에서 Laravel 디렉토리 권한 설정"

date: 2021-07-23T15:35:56+09:00

author: CozyFex

categories:

- laravel

tags:

- laravel

keywords:

- laravel

---

## 유저 그룹 변경

```shell
sudo usermod -a -G www-data $USER
```

## Laravel 디렉토리 소유권 변경

```shell
sudo chgrp -R www-data /home/laravel/www
```

## `storage` 디렉토리 권한 변경

```shell
sudo chmod -R 775 /home/laravel/www/storage
```

## 영구적인 방법

```shell
sudo setfacl -d -R -m u:$USER:rwx,g:www-data:rwx,o:rx /home/laravel/www
```
