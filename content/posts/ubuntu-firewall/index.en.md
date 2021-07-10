---
title: "Ubuntu Firewall"

date: 2021-06-02T23:54:33+09:00

author: CozyFex

categories:

- linux
- ubuntu
- firewall
- ufw

tags:

- linux
- ubuntu
- firewall
- ufw
- allow
- deny
- ip
- port

keywords:

- linux
- ubuntu
- firewall
- ufw
- allow
- deny
- ip
- port

---

## View Status

```shell
sudo ufw status verbose
```

```
Status: inactive
```

## Default

```shell
sudo ufw default allow outgoing
sudo ufw default deny incoming
```

## Enable IPv6

```shell
vi /etc/default/ufw
```

```
...
...
IPV6=yes
...
...
```

## Add Rules

### Service Port

```shell
sudo ufw allow ssh
sudo ufw deny ssh
```

### Protocol

```shell
sudo ufw allow 2323/tcp
sudo ufw allow 1122/upd
sudo ufw allow out 3000/tcp
sudo ufw allow 6783:6784/tcp comment 'CNI'
```

## Advanced Rules

### From

```shell
sudo ufw allow from 192.51.100.10
sudo ufw allow from 192.51.100.0/24
```

### From To

```shell
sudo ufw allow from 198.51.100.10 to any port 22 proto tcp
sudo ufw allow from 198.51.100.10 to 192.156.39.22 port 22 proto tcp
```

## Delete

```shell
sudo ufw delete allow 10250
```

```shell
sudo ufw status numbered
```

```
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 22/tcp                     ALLOW IN    Anywhere
[ 2] 2379:2380/tcp              ALLOW IN    Anywhere
[ 3] 6443/tcp                   ALLOW IN    Anywhere
[ 4] 10250/tcp                  ALLOW IN    Anywhere
[ 5] 10251/tcp                  ALLOW IN    Anywhere
[ 6] 10252/tcp                  ALLOW IN    Anywhere
[ 7] 8080/tcp                   ALLOW IN    Anywhere
[ 8] 16440/tcp                  ALLOW IN    Anywhere
[ 9] 1644/tcp                   ALLOW OUT   Anywhere                   (out)
[10] 22/tcp (v6)                ALLOW IN    Anywhere (v6)
[11] 2379:2380/tcp (v6)         ALLOW IN    Anywhere (v6)
[12] 6443/tcp (v6)              ALLOW IN    Anywhere (v6)
[13] 10250/tcp (v6)             ALLOW IN    Anywhere (v6)
[14] 10251/tcp (v6)             ALLOW IN    Anywhere (v6)
[15] 10252/tcp (v6)             ALLOW IN    Anywhere (v6)
[16] 8080/tcp (v6)              ALLOW IN    Anywhere (v6)
[17] 16440/tcp (v6)             ALLOW IN    Anywhere (v6)
[18] 1644/tcp (v6)              ALLOW OUT   Anywhere (v6)              (out)
```

```shell
# The number of above list
sudo ufw delete 4
```

## Status

```shell
sudo ufw status
sudo ufw status verbose
sudo ufw status numbered
```

## Limit

```shell
sudo ufw limit ssh
```

## Turn on

```shell
sudo ufw enable
```

## Turn off

```shell
sudo ufw disable
```

## System Status

```shell
sudo systemctl status ufw
```




