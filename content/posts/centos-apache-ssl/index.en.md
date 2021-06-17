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

### Check Installed

```shell
sudo yum list installed | grep openssl
```

### Install `openssl`

```shell
sudo yum install openssl
```

## `mod_ssl`

### Check Installed

### Install `mod_ssl`

```shell
sudo yum install mod_ssl
```

### Check Module `mod_ssl.so`

```shell
ll /etc/httpd/modules/mod_ssl.so
```

## Load Module In Apache

```shell
# Main Config File
sudo vi /etc/httpd/conf/httpd.conf

# Or some of apache version
sudo vi /etc/httpd/conf.modules.d/00-ssl.conf
```

```
LoadModule ssl_module modules/mod_ssl.so
```

## Config Apache

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


