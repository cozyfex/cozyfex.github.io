---
title: "Linux Remove Package and Service"

date: 2021-07-04T19:40:30+09:00

author: CozyFex

categories:

- linux
- package

tags:

- linux
- kubernetes
- package
- service
- remove

keywords:

- linux
- kubernetes
- package
- service
- remove

---

## Check Services

```shell
systemctl list-units --type service
```

## Stop Service

```shell
systemctl stop apache2
```

## Disable Service

```shell
systemctl disable apache2
```

## Check Installed Packages

```shell
apt list --installed
```

## Remove Package

```shell
apt remove apache2
```
