---
title: "CentOS Apache SSL"

date: 2021-06-18T00:14:35+09:00

author: CozyFex

categories:

- linux
- centos
- apache
- ssl

tags:

- linux
- centos
- apache
- ssl
- openssl
- mod_ssl

keywords:

- linux
- centos
- apache
- ssl
- openssl
- mod_ssl

---

## `openssl`

### 설치 확인

```shell
sudo yum list installed | grep openssl
```

### `openssl` 설치

```shell
sudo yum install openssl
```

## `mod_ssl`

### 설치 확인

### `mod_ssl` 설치

```shell
sudo yum install mod_ssl
```

### `mod_ssl.so` 모듈 확인

```shell
ll /etc/httpd/modules/mod_ssl.so
```

## Apache 에서 모듈 로드 확인

```shell
# Main Config File
sudo vi /etc/httpd/conf/httpd.conf

# Or some of apache version
sudo vi /etc/httpd/conf.modules.d/00-ssl.conf
```

```
LoadModule ssl_module modules/mod_ssl.so
```

## Apache 설정

```shell
sudo vi /etc/httpd/conf.d/ssl.conf
```

```
SSLEngine on
SSLCertificateFile ssl/ssl.crt
SSLCertificateKeyFile ssl/ssl.key
SSLCertificateChainFile ssl/chain_all.crt
SSLCACertificateFile ssl/chain.crt
```


