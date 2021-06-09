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

## How to Connect Between A and C?

{{<admonition note Network true>}}  
There are two switches as `192.168.1.0` and `192.168.2.0`.  
`192.168.1.0` and `192.168.2.0` are different network environments.  
That means A and C are also different networks.  
At this moment, we need a router to connect between the different networks.  
{{</admonition>}}

## Gateway

{{<admonition note Gateway true>}}  
There's no way to connect between A and C directly.  
So we need a door to connect between them.  
In this case, we have a router.  
We call it `Gateway`.  
{{</admonition>}}

## Default Gateway

{{<admonition note "Default Gateway" true>}}  
Now, we know what is the gateway.  
You can set a gateway as system default.  
Default Gateway means the system use a gateway as default.  
{{</admonition>}}

## Scenario

{{<mermaid>}}

graph TD;

A(Set route to A) --> R(Router)  
C(Set route to C) --> R(router)

{{</mermaid>}}

### Set Route A

#### Add Route

```shell
ip route add 192.168.2.0/24 via 192.168.1.1
```

#### Check Route List

```shell
ip route
ip route list
```

### Set Route C

#### Add Route

```shell
ip route add 192.168.1.0/24 via 192.168.2.1
```

#### Check Route List

```shell
ip route
ip route list
```

### Set Default Gateway

#### In PC A

```shell
ip route add default via 192.168.1.1
```

#### In PC B

```shell
ip route add default via 192.168.2.1
```

## PC as a Route

### Diagram

{{<image src="network-pc-as-a-route.png" width="100%">}}

### How to Connect Between PC A and PC C

#### Ping PC A to PC C

<sub>PC A</sub>

```shell
ping 192.168.2.5
```

```code
Connect: Network is unreachable
```

#### Set Route

<sub>PC A</sub>

```shell
ip route add 192.168.2.0/24 via 192.168.1.6
```

<sub>PC C</sub>

```shell
ip route add 192.168.1.0/24 via 192.168.2.6
```

