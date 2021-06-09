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

## 고려사항

### 목적

#### 교육

- Minikube
- 싱클 노드 클러스터와 kubeadm/GCP/AWS

#### 개발 & 테스트

- 멀티 노드 클러스터 - 싱글 마스터 노드와 멀티 워커 노드
- kubeadm 을 사용하여 셋업하거나 GCP, AWS, AKS 의 빠른제공 사용

#### Hosting Production Applications

- 높은 유효성 멀티 노드 클러스터와 멀티 마스터 노드
- GCP, AWS, 또는 다른 지원 플렛폼에서의 kubeadm 또는 kOps
- 5,000 노드
- 클러스터 당 150,000 PODs
- 300,000 컨테이너
- 노드 당 100 PODS

| Nodes | GCP Type | GCP Spec | AWS Type | AWS Spec |  
|:-:|:-:|:-:|:-:|:-:|  
| 1~5 | n1-standard-1 | 1 vCPU 3.75 GB | m3.medium | 1 vCPU 3.75 GB |  
| 6~10 | n1-standard-2 | 2 vCPU 7.5 GB | m3.large | 2 vCPU 7.5 GB |  
| 11~100 | n1-standard-4 | 4 vCPU 15 GB | m3.xlarge | 4 vCPU 15 GB |  
| 101~250 | n1-standard-8 | 8 vCPU 30 GB | m3.2xlarge | 8 vCPU 30 GB |  
| 251~500 | n1-standard-16 | 16 vCPU 60 GB | c4.4xlarge | 16 vCPU 60 GB |  
| over 500 | n1-standard-32 | 32 vCPU 120 GB | c4.8xlarge | 32 vCPU 120 GB |

### On-Premise 또는 Cloud Service

- On-Premise 에서 kubeadm 사용
- GCP(Google Cloud Platform) 의 GKE(Google Cloud Kubernetes Engine)
- AWS 의 kOps
- Azure 의 AKS(Azure Kubernetes Service)

### Storage

- 높은 성능 - SSD 저장소
- 멀티 병렬 연결 - 네트워크 기반 저장소
- 여러 POD 에서 교차 공유 연결을 위한 영구 공유 볼륨
- 특정 디스크 유형으로 노드 레이블 지정
- 노드 셀렉터를 사용하여 어플리케이션을 노드의 특정 디스크 유형으로 할당

### Nodes

- 가상 또는 물리적 머신
- 최소 4개의 노드 클러스터(워크로드에 따른 크기)
- 마스터 노드 vs 워커 노드
- 리눅스 x86_64 아키텍처
- 마스터 노드는 워크로드를 호스트 할 수 있다.
- 모범 사례는 마스터 노드에 워크로드를 호스트 하지 않는것이다.

### Master Nodes 구조

{{<admonition note "Structure" true>}}  
적어도 2개의 마스터 노드와 3개의 ETCD 를 구성하는것이 좋다.  
{{</admonition>}}

{{<image src="design.png" width="100%">}}

## Kubernetes Infrastructure

### On-Premise vs Cloud Service

{{<admonition note "On-Premise" true>}}  
`On-Premise` 는 자신의 컴퓨터실에 세팅하는 것  
{{</admonition>}}

{{<admonition note "Cloud Service" true>}}  
`AWS`, `GCP`, 또는 `Azure` 등  
{{</admonition>}}

#### Our Choice

{{<admonition note "Our Laptop!" true>}}  
이건 우리가 `Kubernetes` 를 공부하기 위한 것이다. 그래서 우리는 그냥 노트북을 선택한다.  
{{</admonition>}}

### Linux vs Windows

{{<admonition note "Linux" true>}}  
`Kubernetes` 는 리눅스 시스템 기반으로 돌아간다.  
{{</admonition>}}

#### Our Choice

{{<admonition note "Ubuntu" true>}}  
여기서는 `Ubuntu` 를 이용한다.  
{{</admonition>}}

### minikube vs Kubeadm

{{<admonition note "minikube" true>}}  
`minikube` 는 로컬의 `Kubernetes` 이고, `Kubernetes` 를 위한 배움과 개발을 쉽게 하는것에 목표를 한다.  
{{</admonition>}}

{{<admonition note "Kubeadm" true>}}  
Kubeadm 은 `kubeadm init` 과 `kubeadm join` `Kubernetes` 제공하는 클러스터를 생성하기 위한 모범 사례 지름길이다.  
{{</admonition>}}

#### Our Choice

{{<admonition note "Kubeadm" true>}}  
`Kubernetes` 클러스터를 멀티 노드로 구성할 것이다. 그래서 우리는 `kubeadm` 을 사용할 것이다.   
{{</admonition>}}

### Turnkey Solutions vs Hosted Solutions(Managed Solutions)

#### Turnkey Solutions

##### Turnkey Solution 책임

- VM 준비
- VM 설정
- Script 사용하여 클러스터 배포
- VM 관리

##### Turnkey Solution Products

- [OpenShift](https://www.openshift.com/)
- [Cloud Foundry](https://www.cloudfoundry.org/)
- [VMWare Cloud PKS](https://www.vmware.com/)
- [Vagrant](https://www.vagrantup.com/)

#### Hosted Solutions(Managed Solutions)

##### Hosted Solutions 책임

- Kubernetes As A Service
- VM 프로바이더
- Kubernetes 설치 프로바이더
- VM 관리 프로바이더

##### Hosted Solution Products

- [GKE](https://cloud.google.com/kubernetes-engine)(Google Kubernetes Engine)
- [OpenShift Online](https://www.openshift.com/products/online/)
- [AKS](https://docs.microsoft.com/en-us/azure/aks/)(Azure Kubernetes Service)
- [AWS EKS](https://aws.amazon.com/eks/)(AWS Elastic Kubernetes Service)

#### Our Choice

{{<admonition note "VirtualBox" true>}}  
VM 을 `VirtualBox` 로 구성  
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
`weaveworks` 를 사용  
{{</admonition>}}

## HA(High Availability) Kubernetes Cluster

{{<admonition note "HA" true>}}  
HA 를 위해서는 최소 2개의 마스터 노드를 필요로 한다. 당연히 2개 이상의 워커 노드도 필요하다.  
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

- 쉬운 셋업
- 쉬운 관리
- 비교적 적은 서버
- ***실패시 위험***

### External ETCD Topology

{{<image src="design-external-etcd-topology.png" width="100%">}}

- 비교적 덜 위험함
- ***어려운 셋업***
- ***더 많은 서버***

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
ETCD 는 가장 중요한 데이터를 위한 `분산된`, 믿을 수 있는 `key-value 저장소` 분산 시스템이다.  
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
일관된 데이터를 읽는데에는 큰 문제가 없다.  
{{</admonition>}}

{{<image src="design-etcd-read.png" width="100%">}}

##### Write

{{<admonition note "Write" true>}}  
사용자들이 같은 key 를 동시에 쓸때, 문제가 발생한다.  
이 때, 이걸 해결하는 방법으로 `ETCD Leader` 이 있다.  
{{</admonition>}}

{{<image src="design-write-at-same-time.png" width="100%">}}

##### Leader Election - RAFT

{{<admonition note "Majority - 과반수" true>}}  
절반 이상의 투표  
`N/2+1`
{{</admonition>}}

{{<admonition note "Quorum - 정족수" true>}}  
최소 득표 수
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

#### ETCD API Version 설정

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

##### Get 목록

```shell
etcdctl get --prefix --keys-only
```

## Our Design

{{<admonition note "Design" true>}}  
아래 디자인으로 세팅 할 예정이다.  
{{</admonition>}}

{{<image src="design-cluster-design.png" width="100%">}}

