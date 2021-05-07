---
title: "Kubernetes Core Concepts"

date: 2021-05-07T11:30:12+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- k8s
- orchestration
- core concepts

keywords:

- kubernetes
- k8s
- orchestration
- core concepts

---

## Cluster Architecture

Master Node

- Manage
- Plan
- Schedule
- Monitor Nodes

Worker Nodes

- Host Application as Containers

### Kubernetes Architecture

Master Node

- kube-apiserver
- Controller-Manager
- Node-Manager
- Replication-Manager

Worker Nodes

- kubelet is manager of a node
- Kube Proxy

#### kube-api server Execute Process

1. Authenticate User
2. Validate Request
3. Retrieve data
4. Update ETCD
5. Scheduler
6. Kubelet

#### api-server options

```shell
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```

```shell
cat /etc/systemd/system/kube-apiserver.service
```

```shell
ps -aux | grep kube-apiserver
```

### ETCD for Beginner

ETCD is a distributed reliable key-value store that is Simple, Secure & Fast

#### Install ETCD

1. Download Check official [ETCD](https://etcd.io). The direct link is [here](https://etcd.io/docs/v3.4/dl-build/).

2. Run

```shell
./etcd
```

#### Operate ETCD

```shell
./etcdctl set key1 value1
```

```shell
./etcdctl get key1
# value1
```

### ETCD in Kubernetes

#### ETCD Cluster

- Nodes
- PODs
- Configs
- Secrets
- Accounts
- Roles
- Bindings
- Others

#### Setup

##### Manual

```shell
wget DOWNLOAD_URL
```

```shell
# etcd.service

```

##### kubeadm

```shell
kubectl get pods -n kube-system
```

```shell
kebuectl exec etcd-master -n kube-system etcdctl get / --prefix -keys-only
```

##### ETCD Commands

###### To Set Version

```shell
export ETCDCTL_API=3
```

###### ETCD Version 2

```shell
etcdctl backup
etcdctl cluster-health
etcdctl mk
etcdctl mkdir
etcdctl set
```

###### ETCD Version 3

```shell
etcdctl snapshot save 
etcdctl endpoint health
etcdctl get
etcdctl put
```

### kube-controller-manager

#### List

- Node-Controller
- Replication-Controller
- Replicaset
- Service-Account-Controller
- CronJob
- Job-Controller
- Deployment-Controller
- Namespace-Controller
- Endpoint-Controller
- PV-Protection-Controller
- Stateful-Set

#### Main Functions

- Watch Status
- Remediate Situation

#### Option Examples

Node Monitor Period = 5s\
Node Monitor Grace Period = 40s\
POD Eviction Timeout = 5m

#### kube-controller-manager Options

```shell
cat /etc/kubernetes/manifests/kube-controller-manager.yaml
```

```shell
cat /etc/systemd/system/kube-controller-manager.service
```

```shell
ps -aux | grep kube-controller-manager
```

### Kube Scheduler

kube-scheduler just decide which `POD` goes where.

#### Rule

Required CPU: 10

Node: 4

Node: 5

Node: 12

Node: 16

1. Filter Nodes -> Except 4 Nodes
2. Rank Nodes -> Picked Node: 16

#### kube-scheduler Options

```shell
cat /etc/kubernetes/manifests/kube-scheduler.yaml
```

```shell
cat /etc/systemd/system/kube-scheduler.service
```

```shell
ps -aux | grep kube-scheduler
```

### Kubelet

#### Functions

- Register Node
- Create PODs
- Monitor Node and PODs

#### Options

```shell
ps -aux | grep kubelet
```

### Kube Proxy

It's to configure IP from the service name.

