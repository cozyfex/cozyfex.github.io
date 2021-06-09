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

## CNI(Container Network Interface) in CRI(Container Runtime Interface)

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
CNM is CNI of docker.  
CNM is different with others CNI.  
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


## CNI in Kubernetes

### CNI Config in `kubelet`

| Option | Description | Default | Example |  
|:-:|:-|:-|:-|  
| --network-plugin | Plugin | cni | cni |  
| --cni-conf-dir | Plugin config directory | /etc/cni/net.d | /etc/cni/net.d |  
| --cni-bin-dir | Plugin binary directory | /opt/cni/bin | /opt/cni/bin |

### Check Running CNI Configuration

```shell
ps -aux | grep kubelet
```

### Check CNI Plugins

```shell
ls <--cni-bin-dir>

# Example
ls /opt/cni/bin
```

```
bridge dhcp flannel host-local ipvlan loopback macvlan portmap ptp sample tuning
vlan weave-ipam weave-net weave-plugin-2.2.1
```

### Check CNI Config Files

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

### Check Config File

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


