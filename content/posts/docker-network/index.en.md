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
| NONE | `docker run --network none nginx` | The container is isolated. |  
| HOST | `docker run --network host nginx` | The container uses host network. |  
| BRIDGE | `docker run nginx` | The container uses bridge network that. |

## NONE

{{<admonition note NONE true>}}  
The containers are being isolated. So they can't connect out of the container.  
{{</admonition>}}

```shell
docker run --network none nginx
docker run --network none nginx
```

{{<image src="docker-network-none.png" width="100%">}}

## HOST

{{<admonition note HOST true>}}  
The containers are running on host network, so the containers are using same ports between host and containers.  
Below example, the first container is running, but second container is fail to running because it tries to use a port 80 that is used in first container.  
The `nginx` is using a port 80 as a default.  
You can connect to `http://192.168.1.10:80`  
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
The `docker` make a `BRIDGE` for the `docker` network.  
And each container has they own network.  
That means each container can use a 80 port.  
You can connect below links in local network.  
`http://172.17.0.2:80`  
`http://172.17.0.3:80`  
{{</admonition>}}

```shell
docker run nginx
docker run nginx
```

{{<image src="docker-network-bridge.png" width="100%">}}

### How to Work Docker Bridge?

{{<admonition note "Docker Bridge Network" true>}}  
In this section, we will discover the docker bridge network process.  
You can more detail <a href="/categories/network" target="_blank">my past network posts</a>
{{</admonition>}}

### Create a Bridge in Docker Network NS `bridge`

#### Check Docker Namespace List

```shell
docker network ls
```

```
NETWORK ID     NAME      DRIVER    SCOPE
014bb41965bc   bridge    bridge    local
6d0dbe525ff9   host      host      local
004833fb1e77   none      null      local
```

#### Crate Bridge

```shell
ip link add docker0 type bridge
```

#### Check Link

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

### Assign IP to the Bridge

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

### Create a Container and Network Namespace

{{<image src="docker-network-container-ns.png" width="100%">}}

#### Check Network Namespace

```shell
ip netns

b3165c10a92b
```

```shell
docker inspect 56bf13ceac4d | grep -i sandbox

            "SandboxID": "b3165c10a92b50edce4c8aa5f37273e180907ded31",
            "SandboxKey": "/var/run/docker/netns/b3165c10a92b",
```

### Create a vEth(Virtual Ethernet) Pairs(Pipe, Virtual Cabe)

```shell
ip link add eth0@if8 type veth peer name vethbb1c343@if7
```

{{<image src="docker-network-veth.png" width="100%">}}

### Attach vEth to the Bridge

```shell
ip link set vethbb1c343@if7 master docker0
```

{{<image src="docker-network-veth-bridge.png" width="100%">}}

### Attach vEth to the Container Namespace

```shell
ip link set eth0@if8 netns b3165c10a92b
```

{{<image src="docker-network-veth-bridge-ns.png" width="100%">}}

### Assign IP to the Container vEth

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

### Bring the Bridge Up

```shell
ip link set docker0 up
ip link set vethbb1c343@if7 up
ip -n b3165c10a92b link set eth0@if8 up
```

{{<image src="docker-network-up.png" width="100%">}}

### Enable NAT

#### In Local

```shell
curl http://172.17.0.3:80

Welcome to nginx!
```

#### Out of Local

##### Use Bridge Network

```shell
curl http://172.17.0.3:80

curl: (7) Failed to connect... No route to host
```

##### Use Host Network

```shell
curl http://192.168.1.10:8080

curl: (7) Failed to connect... No route to host
```

#### Docker Port Forward

```shell
docker run -p 8080:80 nginx
```

```shell
curl http://192.168.1.10:8080

Welcome to nginx!
```

#### Manually Port Forward

```shell
iptables \
  –t nat \
  -A PREROUTING \
  -j DNAT \
  --dport 8080 \
  --to-destination 172.17.0.3:80
```

#### Check Port Forward List

```shell
iptables -nvL -t nat
```

```
Chain DOCKER (2 references)
target      prot    opt     source      destination
RETURN      all     --      anywhere    anywhere
DNAT        tcp     --      anywhere    anywhere        tcp dpt:8080 to:172.17.0.3:80
```