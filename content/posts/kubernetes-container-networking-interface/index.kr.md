---
title: "Kubernetes Container Networking Interface"

date: 2021-05-30T17:29:12+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- network
- cni
- container
- interface

tags:

- kubernetes
- network
- cni
- container
- interface

keywords:

- kubernetes
- network
- cni
- container
- interface

---

## Network Namespaces

{{<mermaid>}}

graph TD;

A[1. Create Network Namespace] --> B[2. Create Bridge Network/Interface]  
B --> C[3. Create VETH Pairs - Pipe, Virtual Cable]  
C --> D[4. Attach vEth to Namespace]  
D --> E[5. Attach Other vEth to Bridge]  
E --> F[6. Assign IP Addresses]  
F --> G[7. Bring the interfaces up]  
G --> H[8. Enable NAT - IP Masquerade]

{{</mermaid>}}

## CRI(Container Runtime Interface) 에서 CNI(Container Network Interface)

{{<mermaid>}}

graph TD;

DK(docker) --> A  
RKT(rkt) --> A  
MSS(MESOS) --> A  
KB(Kubernetes) --> A

A[1. Create Network Namespace] --> B[2. Create Bridge Network/Interface]  
B --> C[3. Create VETH Pairs - Pipe, Virtual Cable]  
C --> D[4. Attach vEth to Namespace]  
D --> E[5. Attach Other vEth to Bridge]  
E --> F[6. Assign IP Addresses]  
F --> G[7. Bring the interfaces up]  
G --> H[8. Enable NAT - IP Masquerade]

{{</mermaid>}}

## BRIDGE

### Command

```shell
# Structure
bridge add <cid> <namespace>

# Example
bridge add 2e34dcf34 /var/run/netns/2e34dcf34
```

### Bridge Process

{{<mermaid>}}

graph TD;

B[2. Create Bridge Network/Interface]  
B --> C[3. Create VETH Pairs - Pipe, Virtual Cable]  
C --> D[4. Attach vEth to Namespace]  
D --> E[5. Attach Other vEth to Bridge]  
E --> F[6. Assign IP Addresses]  
F --> G[7. Bring the interfaces up]  
G --> H[8. Enable NAT - IP Masquerade]

{{</mermaid>}}

### CRI(Container Runtime Interface) Products Process

{{<mermaid>}}

graph TD;

A[1. Create Network Namespace]

DK(docker) --> A  
RKT(rkt) --> A  
MSS(MESOS) --> A  
KB(Kubernetes) --> A

A --> BG[BRIDGE]

{{</mermaid>}}

### CNM(Container Network Model) for Docker

{{<admonition note Docker true>}}  
CNM 은 도터의 CNI 이다.  
CNM 은 다른 CNI 와 다르다.  
{{</admonition>}}

#### Models

{{<mermaid>}}

graph TD;

B[BRIDGE]  
V[VLAN]  
I[IPVLAN]  
M[MACVLAN]  
W[WINDOWS]

{{</mermaid>}}

{{<mermaid>}}

graph TD;

D[DHCP]
H[host-local]

{{</mermaid>}}

#### Docker Network

```shell
# There's no network type 'cni-bridge' in docker
#docker run --network=cni-bridge nginx

# RUN
docker run --network=none nginx
```

#### Bridge

```shell
bridge add 2e34dcf34 /var/run/netns/2e34dcf34
```

## CNI(Container Network Interface) Products

{{<mermaid>}}

graph TD;

W[weaveworks]
F[flannel]
C[cilium]
V[VMWare NSX]

{{</mermaid>}}

## Kubernetes 에서 CNI

### `kubelet` 에서 CNI 설정

| Option | Description | Default | Example |  
|:-:|:-|:-|:-|  
| --network-plugin | Plugin | cni | cni |  
| --cni-conf-dir | Plugin 설정 디렉토리 | /etc/cni/net.d | /etc/cni/net.d |  
| --cni-bin-dir | Plugin 바이너리 디렉토리 | /opt/cni/bin | /opt/cni/bin |

### 실행중인 CNI 설정 확인

```shell
ps -aux | grep kubelet
```

### CNI 플러그인 확인

```shell
ls <--cni-bin-dir>

# Example
ls /opt/cni/bin
```

```
bridge dhcp flannel host-local ipvlan loopback macvlan portmap ptp sample tuning
vlan weave-ipam weave-net weave-plugin-2.2.1
```

### CNI 설정 파일 확인

```shell
# Structure
ls <--cni-conf-dir>

# Example
ls /etc/cni/net.d
```

```
# Structure
10-bridge.conf
```

### 설정 파일 확인

```shell
cat /etc/cni/net.d/10-bridge.conf
```

```json
{
  "cniVersion": "0.2.0",
  "name": "mynet",
  "type": "bridge",
  "bridge": "cni0",
  "isGateway": true,
  "ipMasq": true,
  "ipam": {
    "type": "host-local",
    "subnet": "10.22.0.0/16",
    "routes": [
      {
        "dst": "0.0.0.0/0"
      }
    ]
  }
}
```


