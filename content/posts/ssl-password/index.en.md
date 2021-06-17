---
title: "SSL Password"

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

## Remove Password of SSL Key

```shell
openssl rsa -in password.key -out nopassword.key
```

## Set Password of SSL Key

```shell
openssl rsa -in nopassword.key -passout pass:'PASSWORD' -out password.key -des3
```