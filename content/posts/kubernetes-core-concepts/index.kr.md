---
title: "Kubernetes 코어 컨셉"

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

## 클러스터 구조

Master Node

- Manage
- Plan
- Schedule
- Monitor Nodes

Worker Nodes

- Host Application as Containers

### Kubernetes 구조

Master Node

- kube-apiserver
- Controller-Manager
- Node-Manager
- Replication-Manager

Worker Nodes

- kubelet is manager of a node
- Kube Proxy

#### kube-api server 실행 프로세스

1. Authenticate User
2. Validate Request
3. Retrieve data
4. Update ETCD
5. Scheduler
6. Kubelet

#### api-server 옵션

```shell
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```

```shell
cat /etc/systemd/system/kube-apiserver.service
```

```shell
ps -aux | grep kube-apiserver
```

### ETCD

ETCD는 신뢰할 수 있는 분산된 간단하고 안전하고 빠른 키-벨류 저장소이다.

#### Install ETCD

1. 다운로드는 공식 사이트를 참고하면 된다. [ETCD](https://etcd.io). The direct link is [here](https://etcd.io/docs/v3.4/dl-build/).

2. 실행

```shell
./etcd
```

#### ETCD 명령

```shell
./etcdctl set key1 value1
```

```shell
./etcdctl get key1
# value1
```

### Kubernetes에서의 ETCD

#### ETCD 클러스터

- Nodes
- PODs
- Configs
- Secrets
- Accounts
- Roles
- Bindings
- Others

#### 설치

##### 수동

```shell
wget DOWNLOAD_URL
```

```shell
# /etc/systemd/system/etcd.service

```

##### kubeadm

```shell
kubectl get pods -n kube-system
```

```shell
kebuectl exec etcd-master -n kube-system etcdctl get / --prefix -keys-only
```

##### ETCD 명령

###### 버전 설정

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

#### 리스트

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

#### 주요 기능

- Watch Status
- Remediate Situation

#### 옵션 예제

Node Monitor Period = 5s\
Node Monitor Grace Period = 40s\
POD Eviction Timeout = 5m

#### kube-controller-manager 옵션

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

#### 규칙

Required CPU: 10

Node: 4

Node: 5

Node: 12

Node: 16

1. Filter Nodes -> Except 4 Nodes
2. Rank Nodes -> Picked Node: 16

#### kube-scheduler 옵션

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

#### 기능

- Register Node
- Create PODs
- Monitor Node and PODs

#### 옵션

```shell
ps -aux | grep kubelet
```

### Kube Proxy

서비스 이름으로 IP를 알 수 있도록 설정한다.
