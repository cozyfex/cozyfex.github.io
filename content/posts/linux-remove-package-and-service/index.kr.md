---
title: "Linux 패키지와 서비스 제거"

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

## Services 확인

```shell
systemctl list-units --type service
```

## Service 정지

```shell
systemctl stop apache2
```

## Service 비활성화

```shell
systemctl disable apache2
```

## 설치된 패키지 확인

```shell
apt list --installed
```

## 패키지 제거

```shell
apt remove apache2
```
