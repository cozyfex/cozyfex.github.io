---
title: "Kubernetes Cluster Design"

date: 2021-06-01T20:18:15+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- cluster
- design

tags:

- kubernetes
- cluster
- design
- considerations

keywords:

- kubernetes
- cluster
- design
- considerations

---

## Kubernetes Cluster Install Tools

- [kubeadmin](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
- [kOps](https://github.com/kubernetes/kops)

## Considerations

### Purpose

#### Education

- Minikube
- Single node cluster with kubeadm/GCP/AWS

#### Development & Testing

- Multi-Node Cluster with a Single Master and Multiple Works
- Setup using kubeadm tool or quick provision on GCP or AWS or AKS

#### Hosting Production Applications

- High Availability Multi-Node Cluster with multiple master nodes.
- kubeadm or kOps on GCP or AWS or other supported platforms.
- Upto 5,000 nodes
- Upto 150,000 PODs in the cluster
- Upto 300,000 total containers
- Upto 100 PODs per node

| Nodes | GCP Type | GCP Spec | AWS Type | AWS Spec |  
|:-:|:-:|:-:|:-:|:-:|  
| 1~5 | n1-standard-1 | 1 vCPU 3.75 GB | m3.medium | 1 vCPU 3.75 GB |  
| 6~10 | n1-standard-2 | 2 vCPU 7.5 GB | m3.large | 2 vCPU 7.5 GB |  
| 11~100 | n1-standard-4 | 4 vCPU 15 GB | m3.xlarge | 4 vCPU 15 GB |  
| 101~250 | n1-standard-8 | 8 vCPU 30 GB | m3.2xlarge | 8 vCPU 30 GB |  
| 251~500 | n1-standard-16 | 16 vCPU 60 GB | c4.4xlarge | 16 vCPU 60 GB |  
| over 500 | n1-standard-32 | 32 vCPU 120 GB | c4.8xlarge | 32 vCPU 120 GB |

### On-Premise or Cloud Service

- Use kubeadm for On-Premise
- GKE(Google Cloud Kubernetes Engine) for GCP(Google Cloud Platform)
- kOps for AWS
- AKS(Azure Kubernetes Service) for Azure

### Storage

- High Performance - SSD Backed Storage
- Multiple Concurrent Connections - Network Based Storage
- Persistent shared volumes for shared access across multiple PODs
- Label nodes with specific disk types
- Use Node Selectors to assign applications to nodes with specific disk types

### Nodes

- Virtual or Physical Machines
- Minimum of 4 Node Cluster(Size based on workload)
- Master vs Worker Nodes
- Linux x86_64 Architecture
- Master nodes can host workloads
- Best practice is to not host workloads on Master nodes

### Master Nodes Structure

{{<admonition note "Structure" true>}}  
The best way is to set 3 ETCDs and 2 Master nodes at least.  
{{</admonition>}}

{{<image src="design.png" width="100%">}}

## Kubernetes Infrastructure

### On-Premise vs Cloud Service

{{<admonition note "On-Premise" true>}}  
`On-Premise` is to set their own computer room.  
{{</admonition>}}

{{<admonition note "Cloud Service" true>}}  
`AWS`, `GCP`, and `Azure` etc.  
{{</admonition>}}

#### Our Choice

{{<admonition note "Our Laptop!" true>}}  
This is for studying `Kubernetes`. So we just choose our laptop.  
{{</admonition>}}

### Linux vs Windows

{{<admonition note "Linux" true>}}  
`Kubernetes` is running under Linux System.  
{{</admonition>}}

#### Our Choice

{{<admonition note "Ubuntu" true>}}  
I'm going to use `Ubuntu`!  
{{</admonition>}}

### minikube vs Kubeadm

{{<admonition note "minikube" true>}}  
`minikube` is local `Kubernetes`, focusing on making it easy to learn and develop for Kubernetes.  
{{</admonition>}}

{{<admonition note "Kubeadm" true>}}  
Kubeadm is a tool built to provide `kubeadm init` and `kubeadm join` as best-practice "fast paths" for creating `Kubernetes` clusters.  
{{</admonition>}}

#### Our Choice

{{<admonition note "Kubeadm" true>}}  
I'm going to build `Kubernetes` cluster with multiple nodes. So I'm going to use `kubeadm`.  
{{</admonition>}}

### Turnkey Solutions vs Hosted Solutions(Managed Solutions)

#### Turnkey Solutions

##### Turnkey Solution Responsibility

- You provision VMs
- You configure VMs
- You use scripts to deploy cluster
- You maintain VMs yourself

##### Turnkey Solution Products

- [OpenShift](https://www.openshift.com/)
- [Cloud Foundry](https://www.cloudfoundry.org/)
- [VMWare Cloud PKS](https://www.vmware.com/)
- [Vagrant](https://www.vagrantup.com/)

#### Hosted Solutions(Managed Solutions)

##### Hosted Solutions Responsibility

- Kubernetes As A Service
- Provider provisions VMs
- Provider installs Kubernetes
- Provider maintains VMs

##### Hosted Solution Products

- [GKE](https://cloud.google.com/kubernetes-engine)(Google Kubernetes Engine)
- [OpenShift Online](https://www.openshift.com/products/online/)
- [AKS](https://docs.microsoft.com/en-us/azure/aks/)(Azure Kubernetes Service)
- [AWS EKS](https://aws.amazon.com/eks/)(AWS Elastic Kubernetes Service)

#### Our Choice

{{<admonition note "VirtualBox" true>}}  
I'm going to build VMs with `VirtualBox`.  
{{</admonition>}}

## Networking Solution

### Network Solution Products

- [CISCO](https://www.cisco.com/)
- [weaveworks](https://www.weave.works/)
- [flannel](https://github.com/flannel-io/flannel)
- [cilium](https://cilium.io/)
- [VMWare NSX](https://www.vmware.com/products/nsx)
- [nuagenetworks](https://www.nuagenetworks.net/)

### Our Choice

{{<admonition note "weaveworks" true>}}  
`weaveworks` is our choice!  
{{</admonition>}}

## HA(High Availability) Kubernetes Cluster

{{<admonition note "HA" true>}}  
We need 2 master nodes at least for HA. Of course, we need more than 2 worker nodes.  
{{</admonition>}}

### Master Nodes

{{<image src="design-2-master-nodes.png" width="100%">}}

#### API Server

{{<image src="design-api-server.png" width="100%">}}

#### Controller Manager

```shell
kube-controller-manager --leader-elect true [other options]
                        --leader-elect-lease-duration 15s
                        --leader-elect-renew-deadline 10s
                        --leader-elect-retry-period 2s
```

{{<image src="design-contoller-manager.png" width="100%">}}

### Stacked Topology

{{<image src="design-stacked-topology.png" width="100%">}}

- Easier to setup
- Easier to manage
- Fewer servers
- ***Risk during failures***

### External ETCD Topology

{{<image src="design-external-etcd-topology.png" width="100%">}}

- Less risky
- ***Harder to setup***
- ***More servers***

### Config ETCD to `kube-apiserver`

{{<admonition note "--etcd-servers" true>}}  
Check `--etcd-servers`  
{{</admonition>}}

```yaml
spec:
  containers:
    - command:
        - kube-apiserver
        - --advertise-address=192.168.49.2
        - --allow-privileged=true
        - --authorization-mode=Node,RBAC
        - --client-ca-file=/var/lib/minikube/certs/ca.crt
        - --enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,DefaultTolerationSeconds,NodeRestriction,MutatingAdmissionWebhook,ValidatingAdmissionWebhook,ResourceQuota
        - --enable-bootstrap-token-auth=true
        - --etcd-cafile=/var/lib/minikube/certs/etcd/ca.crt
        - --etcd-certfile=/var/lib/minikube/certs/apiserver-etcd-client.crt
        - --etcd-keyfile=/var/lib/minikube/certs/apiserver-etcd-client.key
        - --etcd-servers=https://10.240.0.10:2379,https://10.240.0.11:2479
        - --insecure-port=0
        - --kubelet-client-certificate=/var/lib/minikube/certs/apiserver-kubelet-client.crt
        - --kubelet-client-key=/var/lib/minikube/certs/apiserver-kubelet-client.key
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --proxy-client-cert-file=/var/lib/minikube/certs/front-proxy-client.crt
        - --proxy-client-key-file=/var/lib/minikube/certs/front-proxy-client.key
        - --requestheader-allowed-names=front-proxy-client
        - --requestheader-client-ca-file=/var/lib/minikube/certs/front-proxy-ca.crt
        - --requestheader-extra-headers-prefix=X-Remote-Extra-
        - --requestheader-group-headers=X-Remote-Group
        - --requestheader-username-headers=X-Remote-User
        - --secure-port=8443
        - --service-account-issuer=https://kubernetes.default.svc.cluster.local
        - --service-account-key-file=/var/lib/minikube/certs/sa.pub
        - --service-account-signing-key-file=/var/lib/minikube/certs/sa.key
        - --service-cluster-ip-range=10.96.0.0/12
        - --tls-cert-file=/var/lib/minikube/certs/apiserver.crt
        - --tls-private-key-file=/var/lib/minikube/certs/apiserver.key
```

### Our Choice

{{<admonition note "Our Structure" true>}}  
2 stacked topology master nodes  
2 worker nodes  
1 ETCD on host  
{{</admonition>}}

## ETCD in HA

{{<admonition note "Design" true>}}  
ETCD is a `distributed`, reliable `key-value store` for the most critical data of a distributed system.  
{{</admonition>}}

### `key-value store`

#### JSON

```json
{
  "name": "John Doe",
  "age": 43,
  "location": "New York"
}
```

#### YAML

```yaml
name: "John Doe"
age: 43
location: "New Work"
```

#### TOML

```toml
name = "John Doe"
age = 43
location = "New York"
```

### `distributed`

#### Consistent

{{<image src="design-consistent.png" width="100%">}}

##### Read

{{<admonition note "Read" true>}}  
There's no big problem for consistent reading data.  
{{</admonition>}}

{{<image src="design-etcd-read.png" width="100%">}}

##### Write

{{<admonition note "Write" true>}}  
When users write a data that is same key at same time, there's a problem!  
At this time, it is possible to write to only `ETCD Leader`.  
{{</admonition>}}

{{<image src="design-write-at-same-time.png" width="100%">}}

##### Leader Election - RAFT

{{<admonition note "Majority" true>}}  
More than half the votes.  
`N/2+1`
{{</admonition>}}

{{<admonition note "Quorum" true>}}  
Minimum number of the votes.  
`N/2+1`
{{</admonition>}}

###### Quorum

| Instances | Quorum | Fault Tolerance |  
|:-:|:-:|:-:|  
| 1 | 1 | 0 |  
| 2 | 2 | 0 |  
| 3 | 2 | 1 |  
| 4 | 3 | 1 |  
| 5 | 3 | 2 |  
| 6 | 4 | 2 |  
| 7 | 4 | 3 |

###### Odd or Even?

| Managers | Majority | Fault Tolerance |  
|:-:|:-:|:-:|  
| 1 | 1 | 0 |  
| 2 | 2 | 0 |  
| 3 | 2 | 1 |  
| 4 | 3 | 1 |  
| 5 | 3 | 2 |  
| 6 | 4 | 2 |  
| 7 | 4 | 3 |

### Getting Started

```shell
wget -q --https-only 
    \ "https://github.com/coreos/etcd/releases/download/v3.3.9/etcd-v3.3.9-linux-amd64.tar.gz"
tar -xvf etcd-v3.3.9-linux-amd64.tar.gz
mv etcd-v3.3.9-linux-amd64/etcd* /usr/local/bin/
mkdir -p /etc/etcd /var/lib/etcd
cp ca.pem kubernetes-key.pem kubernetes.pem /etc/etcd/
```

### `etcd.service`

{{<admonition note "--initial-cluster" true>}}  
`--initial-cluster peer-1=https://${PEER1_IP}:2380,peer-2=https://${PEER2_IP}:2380`.  
{{</admonition>}}

```
ExecStart=/usr/local/bin/etcd \\
  --name ${ETCD_NAME} \\
  --cert-file=/etc/etcd/kubernetes.pem \\
  --key-file=/etc/etcd/kubernetes-key.pem \\
  --peer-cert-file=/etc/etcd/kubernetes.pem \\
  --peer-key-file=/etc/etcd/kubernetes-key.pem \\
  --trusted-ca-file=/etc/etcd/ca.pem \\
  --peer-trusted-ca-file=/etc/etcd/ca.pem \\
  --peer-client-cert-auth \\
  --client-cert-auth \\
  --initial-advertise-peer-urls https://${INTERNAL_IP}:2380 \\
  --listen-peer-urls https://${INTERNAL_IP}:2380 \\
  --listen-client-urls https://${INTERNAL_IP}:2379,https://127.0.0.1:2379 \\
  --advertise-client-urls https://${INTERNAL_IP}:2379 \\
  --initial-cluster-token etcd-cluster-0 \\
  --initial-cluster peer-1=https://${PEER1_IP}:2380,peer-2=https://${PEER2_IP}:2380 \\
  --initial-cluster-state new \\
  --data-dir=/var/lib/etcd
```

### `etcdctl`

#### Set ETCD API Version

```shell
export ETCDCTL_API=3
```

#### Data Control Commands

##### Put

```shell
etcdctl put name john
```

##### Get

```shell
etcdctl get name
```

```
name
john
```

##### Get List

```shell
etcdctl get --prefix --keys-only
```

## Our Design

{{<admonition note "Design" true>}}  
I'm going to set below design.  
{{</admonition>}}

{{<image src="design-cluster-design.png" width="100%">}}

