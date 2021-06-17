---
title: "Ubuntu Change Locale"

date: 2021-06-17T15:46:58+09:00

author: CozyFex

categories:

- linux
- ubuntu
- locale

tags:

- linux
- ubuntu
- locale
- change locale
- locale-gen
- update-locale

keywords:

- linux
- ubuntu
- locale
- change locale
- locale-gen
- update-locale

---

## Check Current Locale

```shell
locale
```

```
LANG=en_US.UTF-8
LANGUAGE=en_US:en
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```

## Available Locale

```shell
locale -a
```

## Add Locale You Want

```shell
sudo locale-gen ko_KR
sudo locale-gen ko_KR.UTF-8
```

## Update Locale

### `update-locale`

```shell
sudo update-locale LANG=ko_KR.UTF-8 LC_MESSAGES=POSIX
```

### `reboot`

```shell
sudo reboot
```

### `locale`

```shell
locale
```

```
LANG=ko_KR.UTF-8
LANGUAGE=
LC_CTYPE="ko_KR.UTF-8"
LC_NUMERIC="ko_KR.UTF-8"
LC_TIME="ko_KR.UTF-8"
LC_COLLATE="ko_KR.UTF-8"
LC_MONETARY="ko_KR.UTF-8"
LC_MESSAGES=POSIX
LC_PAPER="ko_KR.UTF-8"
LC_NAME="ko_KR.UTF-8"
LC_ADDRESS="ko_KR.UTF-8"
LC_TELEPHONE="ko_KR.UTF-8"
LC_MEASUREMENT="ko_KR.UTF-8"
LC_IDENTIFICATION="ko_KR.UTF-8"
LC_ALL=
```


