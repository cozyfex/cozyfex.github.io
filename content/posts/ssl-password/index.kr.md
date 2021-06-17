---
title: "SSL 비밀번호"

date: 2021-06-18T01:13:50+09:00

author: CozyFex

categories:

- linux
- ssl
- ssl password
- openssl

tags:

- linux
- ssl
- ssl password
- openssl

keywords:

- linux
- ssl
- ssl password
- openssl

---

## SSL Key 비밀번호 제거

```shell
openssl rsa -in password.key -out nopassword.key
```

## SSL Key 비밀번호 설정

```shell
openssl rsa -in nopassword.key -passout pass:'PASSWORD' -out password.key -des3
```