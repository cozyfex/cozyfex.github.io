---
title: "Laravel Directory Permission on Ubuntu"

date: 2021-07-23T15:35:56+09:00

author: CozyFex

categories:

- laravel

tags:

- laravel

keywords:

- laravel

---

## Change User Group

```shell
sudo usermod -a -G www-data $USER
```

## Change Ownership of Laravel Directory

```shell
sudo chgrp -R www-data /home/laravel/www
```

## Change `storage` Directory Permission

```shell
sudo chmod -R 775 /home/laravel/www/storage
```

## Permanent Solution

```shell
sudo setfacl -d -R -m u:$USER:rwx,g:www-data:rwx,o:rx /home/laravel/www
```
