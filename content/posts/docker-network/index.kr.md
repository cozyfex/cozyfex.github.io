---
title: "Docker Network"

date: 2021-05-30T15:10:45+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network
- docker

tags:

- kubernetes
- network
- docker
- none
- host
- bridge

keywords:

- kubernetes
- network
- docker
- none
- host
- bridge

---

## Docker Network Mode

| Mode | Command | Description |  
|:-:|:-|:-|  
| NONE | `docker run --network none nginx` | 격리된 컨테이너 |  
| HOST | `docker run --network host nginx` | 호스트 네트워 사용하는 컨테이너 |  
| BRIDGE | `docker run nginx` | 브릿지 네트워크 사용하는 컨테이너 |

## NONE

{{<admonition note NONE true>}}  
격리된 컨테이너는 컨테이너 외부 접근이 불가능하다.  
{{</admonition>}}

```shell
docker run --network none nginx
docker run --network none nginx
```

{{<image src="docker-network-none.png" width="100%">}}

## HOST

{{<admonition note HOST true>}}  
컨테이너는 호스트 네티워크 상에서 동작하고, 그러므로 해당 컨테이너는 호스트와 같은 포트를 사용한다.  
아래의 예제에서, 첫번째 컨테이너는 동작 하지만, 두번째 컨테이너는 동작하지 않는다. 두번째 컨테이너가 첫번째 컨테이너가 사용중인 80포트를 사용하려고 하기 때문이다.  
`nginx` 는 80 포트를 기본으로 사용한다.  
`http://192.168.1.10:80` 으로 연결 할 수 있다.  
{{</admonition>}}

```shell
# It's UP
docker run --network host nginx

# It's gonna DOWN
docker run --network host nginx
```

{{<image src="docker-network-host.png" width="100%">}}

## BRIDGE

{{<admonition note BRIDGE true>}}  
`docker` 는 컨테이너를 위해 `BRIDGE` 네트워크를 생성한다.  
그리고 각각의 컨테이너는 자기 자신의 네티워크를 구성한다.  
그러므로 각각의 컨테이너는 각자의 80 포트를 사용 할 수 있다.  
아래 예저와 같은 링크들로 접속 해 볼 수 있다.  
{{</admonition>}}

```shell
docker run nginx
docker run nginx
```

{{<image src="docker-network-bridge.png" width="100%">}}

### Docker Bridge 는 어떻게 동작하는가?

{{<admonition note "Docker Bridge Network" true>}}  
이 섹션에서는 도커 브릿지 네트워크 프로세스를 알아볼 것이다.  
더 자세한 사항은 <a href="/categories/network" target="_blank">내 이전의 네트워크 포스트들</a>을 참고하자.  
{{</admonition>}}

### Docker 네트워크 네임스페이스에서 `bridge` 생성

#### Docker Namespace 목록 확인

```shell
docker network ls
```

```
NETWORK ID     NAME      DRIVER    SCOPE
014bb41965bc   bridge    bridge    local
6d0dbe525ff9   host      host      local
004833fb1e77   none      null      local
```

#### Bridge 생성

```shell
ip link add docker0 type bridge
```

#### Link 확인

```shell
ip link
```

```
...
...
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
   link/ether 02:42:88:56:50:83 brd ff:ff:ff:ff:ff:ff
...
...
```

{{<image src="docker-network-create-bridge.png" width="100%">}}

### Bridge 에 IP 할당

```shell
ip addr add 172.17.0.1 dev docker0
```

```shell
ip link
```

```
...
...
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
   link/ether 02:42:88:56:50:83 brd ff:ff:ff:ff:ff:ff
   inet 172.17.0.1/24 brd 172.17.0.255 scope global docker0
...
...
```

{{<image src="docker-network-bridge-assign-ip.png" width="100%">}}

### 컨테이너와 네임스페이스 생성

{{<image src="docker-network-container-ns.png" width="100%">}}

#### Network Namespace 확인

```shell
ip netns

b3165c10a92b
```

```shell
docker inspect 56bf13ceac4d | grep -i sandbox

            "SandboxID": "b3165c10a92b50edce4c8aa5f37273e180907ded31",
            "SandboxKey": "/var/run/docker/netns/b3165c10a92b",
```

### vEth(Virtual Ethernet) Pairs(Pipe, Virtual Cabe) 생성

```shell
ip link add eth0@if8 type veth peer name vethbb1c343@if7
```

{{<image src="docker-network-veth.png" width="100%">}}

### Bridge 를 vEth 에 연결

```shell
ip link set vethbb1c343@if7 master docker0
```

{{<image src="docker-network-veth-bridge.png" width="100%">}}

### 컨테이너 네임스페이스를 vEth 에 연결

```shell
ip link set eth0@if8 netns b3165c10a92b
```

{{<image src="docker-network-veth-bridge-ns.png" width="100%">}}

### 컨테이너 vEth 에 IP 할당

```shell
ip -n b3165c10a92b addr add 172.17.0.3/24 dev eth0@if8
```

```shell
ip -n b3165c10a92b link
```

```
...
...
7: eth0@if8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.3/16 brd 172.17.255.255 scope global eth0 valid_lft forever preferred_lft forever
...
...
```

{{<image src="docker-network-container-ip.png" width="100%">}}

### Bridge 활성화

```shell
ip link set docker0 up
ip link set vethbb1c343@if7 up
ip -n b3165c10a92b link set eth0@if8 up
```

{{<image src="docker-network-up.png" width="100%">}}

### NAT 허용

#### 로컬 내부

```shell
curl http://172.17.0.3:80

Welcome to nginx!
```

#### 로컬 외부

##### Bridge Network 사용

```shell
curl http://172.17.0.3:80

curl: (7) Failed to connect... No route to host
```

##### Host Network 사용

```shell
curl http://192.168.1.10:8080

curl: (7) Failed to connect... No route to host
```

#### Docker Port 포워드

```shell
docker run -p 8080:80 nginx
```

```shell
curl http://192.168.1.10:8080

Welcome to nginx!
```

#### 수동 Port Forward

```shell
iptables \
  –t nat \
  -A PREROUTING \
  -j DNAT \
  --dport 8080 \
  --to-destination 172.17.0.3:80
```

#### Port Forward 목록 확인

```shell
iptables -nvL -t nat
```

```
Chain DOCKER (2 references)
target      prot    opt     source      destination
RETURN      all     --      anywhere    anywhere
DNAT        tcp     --      anywhere    anywhere        tcp dpt:8080 to:172.17.0.3:80
```