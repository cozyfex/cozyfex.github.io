---
title: "Kubernetes High Availability"

date: 2021-06-04T20:06:02+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- keepalived
- haproxy

tags:

- kubernetes
- high availability
- ha
- keepalived
- haproxy
- kubeadm

keywords:

- kubernetes
- high availability
- ha
- keepalived
- haproxy
- kubeadm

---

## Kubernetes 고가용성

{{<admonition note "Kubernetes 고가용성" true>}}  
Kubernetes 에서 고가용성을 위해서는 로드밸런싱 시스템이 필요하다.  
이 [공식 문서](https://github.com/kubernetes/kubeadm/blob/master/docs/ha-considerations.md#options-for-software-load-balancing)를 참고했다.

이 포스트에서는 2개의 마스터 노드에만 집중한다.  
{{</admonition>}}

## 전제조건

{{<admonition note Prerequisites true>}}  
VirtualBox 에서 Ubuntu 20.04.2 LTS 를 사용한다.  
{{</admonition>}}

## `keepalived`

{{<admonition note keepalived true>}}  
`keepalived` 는 헬스 체크를 위한 같은 IP 서브넷의 가상 IP를 제공한다.  
{{</admonition>}}

### `keepalived` 설치

```shell
sudo apt install keepalived
```

### Firewall 설치

#### master01

```shell
sudo ufw allow from 10.0.2.11 comment 'From master02'
```

#### master02

```shell
sudo ufw allow from 10.0.2.10 comment 'From master01'
```

### `keepalived` 설정

#### 설정

```shell
sudo vi /etc/keepalived/keepalived.conf
```

<sub>Template</sub>

```
global_defs {
    router_id LVS_DEVEL
}
vrrp_script check_apiserver {
  script "/etc/keepalived/check_apiserver.sh"
  interval 3
  weight -2
  fall 10
  rise 2
}

vrrp_instance VI_1 {
    state ${STATE}
    interface ${INTERFACE}
    virtual_router_id ${ROUTER_ID}
    priority ${PRIORITY}
    authentication {
        auth_type PASS
        auth_pass ${AUTH_PASS}
    }
    virtual_ipaddress {
        ${APISERVER_VIP}
    }
    track_script {
        check_apiserver
    }
}
```

| Value | Description | Example |  
|:-:|:-|:-:|  
| ${STATE} | `MASTER` or `BACKUP` | `MASTER` |  
| ${INTERFACE} | 네트워크 인터페이스 | `eth0` |  
| ${ROUTER_ID} | 같은 서브넷의 모든 `keepalived` 클러스터 호스트들에서 같아야 한다. | 51 |  
| ${PRIORITY} | `MASTER` 의 값이 `BACKUP` 의 값 보다 높아야 한다. | 110 |  
| ${AUTH_PASS} | 모든 `keepalived` 클러스터 호스트에서 같아야 한다. | pass1122 |  
| ${APISERVER_VIP} | `keepalived` 클러스터 호스트들 같에 협상을 위한 가상 IP 주소이다. | 10.0.2.100 |

<sub>Example for master01</sub>

```
global_defs {
    router_id LVS_DEVEL
}
vrrp_script check_apiserver {
  script "/etc/keepalived/check_apiserver.sh"
  interval 3
  weight -2
  fall 10
  rise 2
}

vrrp_instance VI_1 {
    state MASTER
    interface enp0s3
    virtual_router_id 51
    priority 200
    authentication {
        auth_type PASS
        auth_pass 42
    }
    virtual_ipaddress {
        10.0.2.100
    }
    track_script {
        check_apiserver
    }
}
```

<sub>Example for master02</sub>

```
global_defs {
    router_id LVS_DEVEL
}
vrrp_script check_apiserver {
  script "/etc/keepalived/check_apiserver.sh"
  interval 3
  weight -2
  fall 10
  rise 2
}

vrrp_instance VI_1 {
    state BACKUP
    interface enp0s3
    virtual_router_id 51
    priority 100
    authentication {
        auth_type PASS
        auth_pass 42
    }
    virtual_ipaddress {
        10.0.2.100
    }
    track_script {
        check_apiserver
    }
}
```

#### Track Script

```shell
sudo vi /etc/keepalived/check_apiserver.sh
```

<sub>Template</sub>

```shell
#!/bin/sh
errorExit() {
    echo "*** $*" 1>&2
    exit 1
}

curl --silent --max-time 2 --insecure https://localhost:${APISERVER_DEST_PORT}/ -o /dev/null || errorExit "Error GET https://localhost:${APISERVER_DEST_PORT}/"
if ip addr | grep -q ${APISERVER_VIP}; then
    curl --silent --max-time 2 --insecure https://${APISERVER_VIP}:${APISERVER_DEST_PORT}/ -o /dev/null || errorExit "Error GET https://${APISERVER_VIP}:${APISERVER_DEST_PORT}/"
fi
```

| Value | Description | Example |  
|:-:|:-|:-:|  
| ${APISERVER_VIP} | It is the virtual IP address negotiated between the `keepalived` cluster hosts. | 10.0.2.100 |  
| ${APISERVER_DEST_PORT} | The port through which Kubernetes will talk to the API Server | 1644 |

<sub>Example</sub>

```shell
#!/bin/sh

APISERVER_VIP=10.0.2.100
APISERVER_DEST_PORT=1644

errorExit() {
    echo "*** $*" 1>&2
    exit 1
}

curl --silent --max-time 2 --insecure https://localhost:${APISERVER_DEST_PORT}/ -o /dev/null || errorExit "Error GET https://localhost:${APISERVER_DEST_PORT}/"
if ip addr | grep -q ${APISERVER_VIP}; then
    curl --silent --max-time 2 --insecure https://${APISERVER_VIP}:${APISERVER_DEST_PORT}/ -o /dev/null || errorExit "Error GET https://${APISERVER_VIP}:${APISERVER_DEST_PORT}/"
fi
```

## `haproxy`

{{<admonition note haproxy true>}}  
`haproxy` 는 간단한 스트림 기반 부하 분산 시스템입니다.  
{{</admonition>}}

### `haproxy` 설치

```shell
sudo apt install haproxy
```

### `haproxy` 설정

<sub>Template</sub>

```
# /etc/haproxy/haproxy.cfg
#---------------------------------------------------------------------
# Global settings
#---------------------------------------------------------------------
global
    log /dev/log local0
    log /dev/log local1 notice
    daemon

#---------------------------------------------------------------------
# common defaults that all the 'listen' and 'backend' sections will
# use if not designated in their block
#---------------------------------------------------------------------
defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option http-server-close
    option forwardfor       except 127.0.0.0/8
    option                  redispatch
    retries                 1
    timeout http-request    10s
    timeout queue           20s
    timeout connect         5s
    timeout client          20s
    timeout server          20s
    timeout http-keep-alive 10s
    timeout check           10s

#---------------------------------------------------------------------
# apiserver frontend which proxys to the control plane nodes
#---------------------------------------------------------------------
frontend apiserver
    bind *:${APISERVER_DEST_PORT}
    mode tcp
    option tcplog
    default_backend apiserver

#---------------------------------------------------------------------
# round robin balancing for apiserver
#---------------------------------------------------------------------
backend apiserver
    option httpchk GET /healthz
    http-check expect status 200
    mode tcp
    option ssl-hello-chk
    balance     roundrobin
        server ${HOST1_ID} ${HOST1_ADDRESS}:${APISERVER_SRC_PORT} check
        # [...]
```

| Value | Description | Example |  
|:-:|:-|:-:|  
| ${APISERVER_DEST_PORT} | Kubernetes 가 API Server 와 통신하는 포트이다. | 1644 |  
| ${APISERVER_SRC_PORT} | API Server 인스턴스가 사용하는 포트이다. | 6443 |  
| ${HOST1_ID} | 상징적 이름 | master01 |  
| ${HOST1_ADDRESS} | 해결 주소 (DNS name, IP address) | 10.0.2.10 |

<sub>Example</sub>

```
global
	log /dev/log	local0
	log /dev/log	local1 notice
	chroot /var/lib/haproxy
	stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
	stats timeout 30s
	user haproxy
	group haproxy
	daemon

	# Default SSL material locations
	ca-base /etc/ssl/certs
	crt-base /etc/ssl/private

	# See: https://ssl-config.mozilla.org/#server=haproxy&server-version=2.0.3&config=intermediate
        ssl-default-bind-ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384
        ssl-default-bind-ciphersuites TLS_AES_128_GCM_SHA256:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256
        ssl-default-bind-options ssl-min-ver TLSv1.2 no-tls-tickets

defaults
	log	global
	mode	http
	option	httplog
	option	dontlognull
        timeout connect 5000
        timeout client  50000
        timeout server  50000
	errorfile 400 /etc/haproxy/errors/400.http
	errorfile 403 /etc/haproxy/errors/403.http
	errorfile 408 /etc/haproxy/errors/408.http
	errorfile 500 /etc/haproxy/errors/500.http
	errorfile 502 /etc/haproxy/errors/502.http
	errorfile 503 /etc/haproxy/errors/503.http
	errorfile 504 /etc/haproxy/errors/504.http

#---------------------------------------------------------------------
# apiserver frontend which proxys to the control plane nodes
#---------------------------------------------------------------------
frontend apiserver
    bind *:1644
    mode tcp
    option tcplog
    default_backend apiserver

#---------------------------------------------------------------------
# round robin balancing for apiserver
#---------------------------------------------------------------------
backend apiserver
    option httpchk GET /healthz
    http-check expect status 200
    mode tcp
    option ssl-hello-chk
    balance     roundrobin
        server master01 10.0.2.10:6443 check
        server master02 10.0.2.11:6443 check
```

## `haproxy` 와 `keepalived` 활성화

```shell
sudo systemctl enable haporyx --now
sudo systemctl enable keepalived --now
```

## Test

### master01 의 IP Status

```shell
ip -br a
```

```
lo               UNKNOWN        127.0.0.1/8 ::1/128
enp0s3           UP             10.0.2.10/24 10.0.2.100/24 fe80::a00:27ff:fe48:484d/64
docker0          DOWN           172.17.0.1/16
```

### master02 의 IP Status

```shell
ip -br a
```

```
lo               UNKNOWN        127.0.0.1/8 ::1/128
enp0s3           UP             10.0.2.11/24 fe80::a00:27ff:fe44:9d58/64
docker0          DOWN           172.17.0.1/16
```

### master01 에서 `keepalived` 중지

```shell
sudo systemctl stop keepalived
```

### master01 의 IP Status

```shell
ip -br a
```

```
lo               UNKNOWN        127.0.0.1/8 ::1/128
enp0s3           UP             10.0.2.10/24 fe80::a00:27ff:fe48:484d/64
docker0          DOWN           172.17.0.1/16
```

### master02 의 IP Status

```shell
ip -br a
```

```
lo               UNKNOWN        127.0.0.1/8 ::1/128
enp0s3           UP             10.0.2.11/24 10.0.2.100/24 fe80::a00:27ff:fe44:9d58/64
docker0          DOWN           172.17.0.1/16
```

