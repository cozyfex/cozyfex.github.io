# Linux 패키지와 서비스 제거


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

