---
title: "Linux CentOS Firewalld"

date: 2021-05-17T17:43:32+09:00

categories:

- linux
- centos
- security
- firewall

tags:

- linux
- centos
- security
- firewall
- firewalld
- port

keywords:

- linux
- centos
- security
- firewall
- firewalld
- port

---

## `Firewalld`란?

{{<admonition note firewalld true>}}  
`Firewalld` 은 네트워크 연결이나 인터페이스의 신뢰된 레벨로 정의된 네트워크/방화벽 존들을 위해 다이나믹한 관리된 방화벽을 제공한다.

IPv4, IPv6 방화벽 설정, 이더넷 브릿지, IP 셋을 제공한다.

분리된 실행환경, 영구 설정 옵션을 제공한다.

서비스나 어플리케이션의 방화벽 규칙을 직접적으로 추가할 수 있는 인터페이스를 제공한다.

자세한 사항은 [Firewalld](https://firewalld.org/) 링크를 통해 확인하면 된다.  
{{</admonition>}}

## `Firewalld` 설치와 실행

```shell
# Install
yum install firewalld

# Start
systemctl start firewalld

# Enable
systemctl enable firewalld
```

## `Firewalld` 설정

### 기본 설정파일들 위치

<sub>`/usr/lib/firewalld/`</sub>

```shell
cd /usr/lib/firewalld/
```

### 시스템별 설정파일들 위치

<sub>`/etc/firewalld/`</sub>

```shell
cd /etc/firewalld/
```

#### Application 설정 파일

<sub>`/etc/firewalld/firewalld.conf`</sub>

```shell
vi /etc/firewalld/firewalld.conf
```

##### DefaultZone

<sub>DefaultZone</sub>

```shell
DefaultZone=public
```

##### 커스터마이즈된 설정 파일

<sub>public</sub>

```shell
vi /etc/firewalld/zones/public.xml
```

<sub>/etc/firewalld/zones/public.xml</sub>

```xml
<?xml version="1.0" encoding="utf-8"?>
<zone>
    <short>Public</short>
    <description>
        For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.
    </description>
    <service name="dhcpv6-client"/>
    <service name="http"/>
    <service name="ssh"/>
    <service name="https"/>
</zone>
```

<sub>custom</sub>

```shell
vi /etc/firewalld/zones/custom.xml
```

## Service Reload

```shell
firewall-cmd --reload
```

## Zone

### 미리 정의된 Zone 목록

```shell
firewall-cmd --get-zones
```

### 모든 Zone 상세정보

```shell
firewall-cmd --list-all-zones
```

### Default Zone 확인

```shell
firewall-cmd --get-default-zone
```

### Active Zone 확인

```shell
firewall-cmd --get-active-zones
```

### Default Zone 설정

```shell
firewall-cmd --set-default-zone=webserver
```

### 새 Zone 추가

```shell
firewall-cmd --permanent --new-zone=webserver
```

### Zone 삭제

```shell
firewall-cmd --permanent --delete-zone=webserver
```

## Service

### Service 목록

```shell
firewall-cmd --get-services
```

### 영구적 Service 목록

```shell
firewall-cmd --permanent --list-all --zone=public
```

### Service 추가

```shell
firewall-cmd --permanent --zone=public --add-service=http
firewall-cmd --permanent --zone=public --add-service=https
```

### Service 제거

```shell
firewall-cmd --permanent --zone=public --remove-service=http
```

## Port

{{<admonition note Note true>}}  
`Port` 를 추가, 변경 삭제한 후에는 `--reload` 를 해야한다.  
{{</admonition>}}

### Port 추가

```shell
# One Port
firewall-cmd --permanent --zone=public --add-port=8080/tcp

# Range Ports
firewall-cmd --permanent --zone=public --add-port=4000-4100/tcp
```

### Port 제거

```shell
# One Port
firewall-cmd --permanent --zone=public --remove-port=8080/tcp

# Range Ports
firewall-cmd --permanent --zone=public --remove-port=4000-4100/tcp
```

## IP

{{<admonition note Note true>}}  
`IP` 를 추가, 변경 삭제한 후에는 `--reload` 를 해야한다.  
{{</admonition>}}

### IP 추가

```shell
firewall-cmd --permanent --zone=public --add-source=192.168.1.0/24 --add-port=22/tcp
```

### IP 변경

```shell
firewall-cmd --permanent --zone=public --change-source=192.168.1.0/24
```

### IP 제거

```shell
firewall-cmd --permanent --zone=public --remove-source=192.168.1.0/24
```