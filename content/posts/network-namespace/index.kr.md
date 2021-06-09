---
title: "Network Namespace"

date: 2021-05-29T17:48:11+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network

tags:

- kubernetes
- network
- namespace

keywords:

- kubernetes
- network
- namespace

---

## Process Namespace

```shell
ps aux
```

```
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.1  21852 11328 ?        Ss   05:34   0:01 /sbin/init
root        1659  0.0  0.0    968     4 ?        Ss   05:34   0:00 /pause
```

{{<admonition note PID true>}}  
위의 `PID` 는 프로세스의 네임스페이스이다.  
위의 예제에서는, 1 과 16759 다.  
{{</admonition>}}

## Network Namespace

{{<admonition note "Routing Table" true>}}  
라우팅 테이블 또는 RIB(Routing Information Base)는 특정 네트워크 대상에 대한 경로를 나열하는 라우터 또는 네트워크 호스트에 저장된 데이터 테이블이며 경우에 따라 해당 경로와 관련된 메트릭(거리)의 나열이다.    
{{</admonition>}}

{{<admonition note "ARP Table" true>}}  
ARP(Address Resolution Protocol)는 호스트의 IP 주소만 알 때, 호스트의 링크 레이어(MAC)을 찾는 방법이다.  
{{</admonition>}}

### Network NS 생성

```shell
ip netns add red
```

{{<image src="network-ns-red.png" width="100%">}}

```shell
ip netns add blue
```

{{<image src="network-ns-red-and-blue.png" width="100%">}}

```shell
ip netns

red
blue
```

### Network NS 영역에서 실행

#### Ethernet 목록 확인

```shell
ip link
```

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc state UNKNOWN mode DEFAULT group default qlen 1000 link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc state UP mode DEFAULT qlen 1000 link/ether 02:42:ac:11:00:08 brd ff:ff:ff:ff:ff:ff
```

#### 특정 NS 영역에서 실행

##### `ip link`

```shell
# Full Command
ip netns exec red ip link

# Short Command
ip -n red link

# 'netns exec red ip' = '-n red'
```

##### `arp`

```shell
# Default NS
arp

# red NS
ip netns exec red arp
```

```
Address         HWtype      HWaddress           Flags   Mask    Iface
172.17.0.21     ether       02:42:ac:11:00:15   C               eth0
172.16.0.8      ether       06:fe:d3:b5:59:65   C               eth0
_gateway        ether       02:42:d5:7a:84:8e   C               eth0
host01          ether       02:42:ac:11:00:1c   C               eth0
```

#### `route`

```shell
# Default NS
route

# red NS
ip netns exec red route
```

```
Kernel IP routing table
Destination     Gateway     Genmask         Flags   Metric  Ref Use Iface
default         _gateway    0.0.0.0         UG      202     0   0   eth0
172.17.0.0      0.0.0.0     255.255.0.0     U       202     0   0   eth0
172.17.0.0      0.0.0.0     255.255.255.0   U       0       0   0   docker0
```

## Virtual Ethernet

### `veth` 생성

```shell
ip link add veth-red type veth peer name veth-blue
```

{{<image src="network-ns-veth-red-blue.png" width="100%">}}

### `veth-red` 를 NS `red` 에 설정

```shell
ip link set veth-red netns red
```

{{<image src="network-ns-veth-red.png" width="100%">}}

### `veth-blue` 를 NS `blue` 에 설정

```shell
ip link set veth-blue netns blue
```

{{<image src="network-ns-veth-blue.png" width="100%">}}

### Virtual Ethernet 에 IP 할당

```shell
ip -n red addr add 192.168.15.1 dev veth-red
ip -n blue addr add 192.168.15.2 dev veth-blue
```

{{<image src="network-ns-ip.png" width="100%">}}

### Virtual Ethernet 활성화

```shell
ip -n red  link set veth-red up
ip -n blue link set veth-blue up
```

{{<image src="network-ns-up.png" width="100%">}}

### Ping

```shell
ip netns exec red ping 192.168.15.2

PING 192.168.15.2 (192.168.15.2) 56(84) bytes of data.
64 bytes from 192.168.15.2: icmp_seq=1 ttl=64 time=0.026 ms
```

### ARP

#### `red` ARP

```shell
ip netns exec red arp
```

```
Address         HWtype  HWaddress           Flags   Mask    Iface
192.168.15.2    ether   ba:b0:6d:68:09:e9   C               veth-red
```

##### `red` ARP Table

| IP | MAC |  
|:-:|:-:|  
| 192.168.15.2 | ba:b0:6d:68:09:e9 |

#### `blue` ARP

```shell
ip netns exec blue arp
```

```
Address         HWtype  HWaddress           Flags   Mask    Iface
192.168.15.1    ether   7a:9d:9b:c8:3b:7f   C               veth-blue
```

##### `blue` ARP Table

| IP | MAC |  
|:-:|:-:|  
| 192.168.15.1 | 7a:9d:9b:c8:3b:7f |

#### Host ARP

```shell
arp
```

```
Address       HWtype    HWaddress           Flags   Mask    Iface
192.168.1.3   ether     52:54:00:12:35:03   C               eth0
192.168.1.4   ether     52:54:00:12:35:04   C               eth0
```

##### Host ARP Table

| IP | MAC |  
|:-:|:-:|  
| 192.168.1.3 | 52:54:00:12:35:03 |  
| 192.168.1.4 | 52:54:00:12:35:04 |

## Linux Bridge

### Bridge 추가

```shell
ip link add v-net-0 type bridge
```

{{<image src="network-ns-bridge.png" width="100%">}}

### Bridge 상태 확인

```shell
ip link
```

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 02:0d:31:14:c7:a7 brd ff:ff:ff:ff:ff:ff
6: v-net-0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 06:9d:69:52:6f:61 brd ff:ff:ff:ff:ff:ff
```

{{<admonition note Bridge true>}}  
`state DOWN` 을 확인할 수 있다.  
{{</admonition>}}

### Bring Bridge Up

```shell
ip link set dev v-net-0 up
```

{{<image src="network-ns-bridge-up.png" width="100%">}}

### Virtual Ethernet 삭제

```shell
ip -n red link del veth-red
```

{{<image src="network-ns-bridge-without-veth.png" width="100%">}}

### Virtual Ethernet 생성

#### Red Bridge Virtual Ethernet

```shell
ip link add veth-red type veth peer name veth-red-br
```

{{<image src="network-ns-bridge-red-veth.png" width="100%">}}

#### Blue Bridge Virtual Ethernet

```shell
ip link add veth-blue type veth peer name veth-blue-br
```

{{<image src="network-ns-bridge-blue-veth.png" width="100%">}}

### `veth-red` 를 NS `red` 에 연결

```shell
ip link set veth-red netns red
```

{{<image src="network-ns-bridge-red-veth-red.png" width="100%">}}

### `veth-red-br` 를 Bridge `v-net-0` 에 연결

```shell
ip link set veth-red-br master v-net-0
```

{{<image src="network-ns-bridge-red-br.png" width="100%">}}

### `veth-blue` 를 NS `blue` 에 연결

```shell
ip link set veth-blue netns blue
```

{{<image src="network-ns-bridge-blue-veth-blue.png" width="100%">}}

### `veth-blue-br` 를 Bridge `v-net-0` 에 연결

```shell
ip link set veth-blue-br master v-net-0
```

{{<image src="network-ns-bridge-blue-br.png" width="100%">}}

### Virtual Ethernet 에 IP 할당

```shell
ip -n red addr add 192.168.15.1 dev veth-red
ip -n blue addr add 192.168.15.2 dev veth-blue
```

{{<image src="network-ns-bridge-ip.png" width="100%">}}

### Virtual Ethernet 활성화

```shell
ip -n red link set veth-red up
ip -n blue link set veth-blue up
```

{{<image src="network-ns-bridge-veth-up.png" width="100%">}}

### `v-net-0` 에 IP 할당

#### Ping Test

```shell
ping 192.168.15.1

Not Reachable!
```

#### IP 할당

```shell
ip addr add 192.168.15.5/24 dev v-net-0
```

{{<image src="network-ns-bridge-net-ip.png" width="100%">}}

#### Ping Test Again

```shell
ping 192.168.15.1

PING 192.168.15.1 (192.168.15.1) 56(84) bytes of data.
64 bytes from 192.168.15.1: icmp_seq=1 ttl=64 time=0.026 ms
```

## LAN(Local Area Network)

### LAN Diagram

{{<image src="network-ns-bridge-base.png" width="100%">}}

### NS `blue` 에서 LAN 으로

#### 연결 확인

```shell
ip netns exec blue ping 192.168.1.3

Connect: Network is unreachable
```

```shell
ip netns exec blue route

Destination   Gateway   Genmask         Flags   Metric  Ref   Use   Iface
192.168.15.0  0.0.0.0   255.255.255.0   U       0       0     0     veth-blu
```

#### NS `blue` 에 Route(Gateway) 추가

```shell
ip netns exec blue ip route add 192.168.1.0/24 via 192.168.15.5 

Destination   Gateway         Genmask         Flags   Metric  Ref   Use   Iface
192.168.15.0  0.0.0.0         255.255.255.0   U       0       0     0     veth-blu
192.168.1.0   192.1168.15.5   255.255.255.0   UG      0       0     0     veth-blu
```

{{<image src="network-ns-bridge-gateway.png" width="100%">}}

#### NS `blue` 에서 LAN 으로 Ping

```shell
ip netns exec blue ping 192.168.1.3

PING 192.168.1.3 (192.168.1.3) 56(84) bytes of data.
```

#### NAT 추가

```shell
iptables -t nat -A POSTROUTING -s 192.168.15.0/24 -j MASQUERADE
```

{{<image src="network-ns-bridge-nat.png" width="100%">}}

#### NS `blue` 에서 LAN 으로 Ping Again

```shell
ip netns exec blue ping 192.168.1.3

PING 192.168.1.3 (192.168.1.3) 56(84) bytes of data.
64 bytes from 192.168.1.3: icmp_seq=1 ttl=63 time=0.587 ms
64 bytes from 192.168.1.3: icmp_seq=2 ttl=63 time=0.466 ms
```

### NS `blue` 에서 WAN 으로

#### NS `blue` 에서 WAN 으로 Ping

```shell
ip netns exec blue ping 8.8.8.8

Connect: Network is unreachable
```

#### NS `blue` Route 확인

```shell
ip netns exec blue route

Destination     Gateway         Genmask         Flags   Metric  Ref   Use   Iface
192.168.15.0    0.0.0.0         255.255.255.0   U       0       0     0     veth-blue
192.168.1.0     192.168.15.5    255.255.255.0   UG      0       0     0     veth-blue
```

#### Default Gateway 추가

```shell
ip netns exec blue ip route add default via 192.168.15.5
```

{{<image src="network-ns-bridge-default-gateway.png" width="100%">}}

```shell
ip netns exec blue route

Destination     Gateway         Genmask         Flags   Metric  Ref   Use   Iface
192.168.15.0    0.0.0.0         255.255.255.0   U       0       0     0     veth-blue
192.168.1.0     192.168.15.5    255.255.255.0   UG      0       0     0     veth-blue
Default         192.168.15.5    255.255.255.0   UG      0       0     0     veth-blue
```

#### NS `blue` 에서 WAN 으로 Ping Again

```shell
ip netns exec blue ping 8.8.8.8

64 bytes from 8.8.8.8: icmp_seq=1 ttl=63 time=0.587 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=63 time=0.466 ms
```
