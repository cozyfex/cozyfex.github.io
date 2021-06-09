---
title: "Network Routing"

date: 2021-05-29T15:08:40+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network

tags:

- kubernetes
- network
- route

keywords:

- kubernetes
- network
- route

---

## Routing Diagram

{{<image src="network-routing.png" width="100%">}}

## A와 C를 어떻게 연결할까?

{{<admonition note Network true>}}
`192.168.1.0` 과 `192.168.2.0` 2개의 스위치들이 있다.  
`192.168.1.0` 과 `192.168.2.0` 는 다른 네트워크 환경에 있다.  
이 말은, A와 C가 서로 다른 네트워크에 있다는것이다.  
이 부분에서, 서로 다른 네트워크 간 연결을 위해서 라우터가 필요하다.  
{{</admonition>}}

## Gateway

{{<admonition note Gateway true>}}  
A와 C를 직접 연결 할 방법은 없다.  
그래서 그들 간 연결을 위해서는 문이 필요하다.  
여기서 우리에겐 라우터가 있다.  
이 역할을 하는 라우터를 `Gateway` 라 부른다.   
{{</admonition>}}

## Default Gateway

{{<admonition note "Default Gateway" true>}}  
이제, 라우터가 무엇인지 알았다.  
게이트웨이를 시스템 기본으로 설정 할 수 있다.  
Default Gateway 는 시스템이 기본으로 사용하는 게이트웨이를 뜻한다.  
{{</admonition>}}

## Scenario

{{<mermaid>}}

graph TD;

A(Set route to A) --> R(Router)  
C(Set route to C) --> R(router)

{{</mermaid>}}

### Route A 설정

#### Route 추가

```shell
ip route add 192.168.2.0/24 via 192.168.1.1
```

#### Route 목록 확인

```shell
ip route
ip route list
```

### Route C 설정

#### Route 추가

```shell
ip route add 192.168.1.0/24 via 192.168.2.1
```

#### Route 목록 확인

```shell
ip route
ip route list
```

### Default Gateway 설정

#### PC A 에서

```shell
ip route add default via 192.168.1.1
```

#### PC B 에서

```shell
ip route add default via 192.168.2.1
```

## PC 를 라우트같이

### Diagram

{{<image src="network-pc-as-a-route.png" width="100%">}}

### PC A 와 PC C 를 어떻게 연결하나?

#### PC A 에서 PC C 로 Ping

<sub>PC A</sub>

```shell
ping 192.168.2.5
```

```code
Connect: Network is unreachable
```

#### Route 설정

<sub>PC A</sub>

```shell
ip route add 192.168.2.0/24 via 192.168.1.6
```

<sub>PC C</sub>

```shell
ip route add 192.168.1.0/24 via 192.168.2.6
```

