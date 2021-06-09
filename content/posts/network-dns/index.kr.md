---
title: "Network DNS"

date: 2021-05-29T16:11:29+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network

tags:

- kubernetes
- network
- dns

keywords:

- kubernetes
- network
- dns

---

## Ping to PC A

{{<image src="network-hosts.png" width="100%">}}

### Ping to IP

```shell
ping 192.168.1.11

Reply from 192.168.1.11: bytes=32 time=4ms TTL=117
Reply from 192.168.1.11: bytes=32 time=4ms TTL=117
```

### Ping to Name

```shell
ping db

ping: unknown host db
```

## Config `/etc/hosts`

### Private Name

#### In PC B

```shell
hostname

host-2
```

#### In PC A

##### Ping to Name

```shell
ping db

ping: unknown host db
```

##### Append to `/etc/hosts`

```shell
echo "192.168.1.11    db" >> /etc/hosts
```

##### Ping to Name Again

```shell
ping db

PING db (192.168.1.11) 56(84) bytes of data.
64 bytes from db (192.168.1.11): icmp_seq=1 ttl=64 time=0.052 ms
64 bytes from db (192.168.1.11): icmp_seq=2 ttl=64 time=0.079 ms
```

### Public Domain

#### Ping to Google

```shell
ping www.google.com

PING www.google.com (172.217.174.100): 56 data bytes
64 bytes from 172.217.174.100: icmp_seq=0 ttl=116 time=61.795 ms
64 bytes from 172.217.174.100: icmp_seq=1 ttl=116 time=32.837 ms
64 bytes from 172.217.174.100: icmp_seq=2 ttl=116 time=83.736 ms
```

#### Append to `/etc/hosts`

```shell
echo "192.168.1.11    db" >> /etc/hosts
```

#### Ping to Google Again

```shell
ping www.google.com

PING www.google.com (192.168.1.11) 56(84) bytes of data.
64 bytes from www.google.com (192.168.1.11): icmp_seq=1 ttl=64 time=0.052 ms
64 bytes from www.google.com (192.168.1.11): icmp_seq=2 ttl=64 time=0.079 ms
```

## Name Resolution

{{<admonition note "Name Resolution" true>}}  
아래의 시스템과 호스트들은 각 PC들 끼리 연결 할 수 있다.  
그러나 큰 문제가 있다.  
`/etc/hosts` 에 추가나 변경이 필요할 경우, 각각 PC의 `/etc/hosts` 파일을 변경해 줘야 한다.   
{{</admonition>}}

### System Diagram

{{<image src="network-name-resolution.png" width="100%">}}

### `/etc/hosts`

```shell
192.168.1.10  web
192.168.1.11  db
192.168.1.12  nfs
192.168.1.21  db-1
192.168.1.22  nfs-1
192.168.1.23  web-1
192.168.1.51  db-2
192.168.1.53  nfs-2
192.168.1.72  web-2
192.168.1.73  db-3
192.168.1.74  nfs-3
192.168.1.75  web-3
192.168.1.94  db-4
192.168.1.96  nfs-4
192.168.1.101  web-4
```

## DNS

{{<admonition note "Domain Name System" true>}}  
Name Resolution System 의 문제점에 대해서 이야기 했었다.  
`DNS` 가 시스템에서 호스트를 관리하기 위한 해결책이다.  
{{</admonition>}}

### DNS Diagram

{{<image src="network-dns.png" width="100%">}}

### DNS Server Config

```shell
192.168.1.10  web
192.168.1.11  db
192.168.1.12  nfs
192.168.1.21  db-1
192.168.1.22  nfs-1
192.168.1.23  web-1
192.168.1.51  db-2
192.168.1.53  nfs-2
192.168.1.72  web-2
192.168.1.73  db-3
192.168.1.74  nfs-3
192.168.1.75  web-3
192.168.1.94  db-4
192.168.1.96  nfs-4
192.168.1.101  web-4
```

### DNS Client Config

#### `/etc/resolv.conf`

```shell
cat /etc/resolv.conf

nameserver 192.168.1.100
```

#### `/etc/nsswitch.conf`

```shell
cat /etc/nsswitch.conf
```

```shell
...
hosts:          files dns
...
```

## Domain Names

### Domain System Example

{{<mermaid>}}

graph TD;

D(.) --> C(.com)  
C --> G(google)  
G --> M(mail)  
G --> DV(drive)
G --> W(www)
G --> MS(maps)
G --> A(apps)

{{</mermaid>}}

### Request `apps.google.com`

```shell
ping apps.google.com

PING www3.l.google.com (216.58.220.142): 56 data bytes
64 bytes from 216.58.220.142: icmp_seq=0 ttl=116 time=37.414 ms
64 bytes from 216.58.220.142: icmp_seq=1 ttl=116 time=44.157 ms
```

{{<mermaid>}}

graph TD;

APPS(apps.google.com) --> ORG(Organization DNS)  
ORG --> CC(Return Cache 216.58.220.142)  
ORG --> R(Root . DNS)  
R --> DC(.com DNS)  
DC --> G(Google DNS)
G --> GR(Return 216.58.220.142)

{{</mermaid>}}

## DNS Record Types

| Type | Description | Example |  
|:-:|:-:|:-:|  
| A | IPv4 | 192.168.1.1 |  
| AAA | IPv6 | 2001:0db8:85a3:0000:0000:8a2e:0370:7334 |  
| CNAME | Domain Name | test.web-server.com,test.api-server.com |

## Tools

### `nslookup`

```shell
nslookup www.google.com
```

```
Server:		192.168.65.2
Address:	192.168.65.2#53

Non-authoritative answer:
Name:	www.google.com
Address: 216.58.220.132
Name:	www.google.com
Address: 2404:6800:4004:810::2004
```

### `dig`

```shell
dig www.google.com
```

```
; <<>> DiG 9.16.1-Ubuntu <<>> www.google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 56513
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;www.google.com.			IN	A

;; ANSWER SECTION:
www.google.com.		250	IN	A	172.217.31.164

;; Query time: 6 msec
;; SERVER: 192.168.65.2#53(192.168.65.2)
;; WHEN: Sat May 29 08:36:57 UTC 2021
;; MSG SIZE  rcvd: 48
```

