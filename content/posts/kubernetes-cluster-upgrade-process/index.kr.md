---
title: "Kubernetes 클러스터 업그레이드 프로세스"

date: 2021-05-17T21:41:08+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- cluster maintenance
- cluster upgrade process

keywords:

- kubernetes
- cluster maintenance
- cluster upgrade process

---

## Kubernetes 코어 컴포넌트 버전 명명 규칙

| 컴포넌트 | 버전 명명 규칙 | 예제 |  
|:-:|:-:|:-:|  
| kube-apiserver | X | v1.10 | | controller-manager | X-1 | v1.9 or v1.10 |  
| kube-scheduler | X-1 | v1.9 or v1.10 |  
| kubelet | X-2 | v1.8 or v1.9 or v.110 |  
| kube-proxy | X-2 | v1.8 or v1.9 or v.110 |  
| kubectl | X+1 > X-1 | v1.10 |

## Kubernetes 지원 버전들

{{<admonition note Versions true>}}  
쿠버네티스는 최근 3개의 마이너 버전들만 지원한다.  
{{</admonition>}}

| 버전 | 지원 여부 |  
|:-:|:-:|  
| v1.10 | <span style="color: red;">지원 안함</span> |  
| v1.11 | <span style="color: red;">지원 안함</span> |  
| v1.12 | <span style="color: green;">지원</span> |  
| v1.13 | <span style="color: green;">지원</span> |  
| v1.14 | <span style="color: green;">지원</span> |

## 업그레이드 추천 프로세스

{{<admonition note "추천" true>}}  
쿠버네티스는 한번에 한개의 마이너 버전 업그레이드를 추천한다.  
여기서는, v1.10 을 v1.14 로 업그레드를 가정한다.  
{{</admonition>}}

### 첫번째(v1.10 -> v1.11)

{{<mermaid>}}  
graph LR;  
v0(v1.10) --> v1(v1.11)  
{{</mermaid>}}

### 두번째(v1.11 -> v1.12)

{{<mermaid>}}  
graph LR;  
v1(v1.11) --> v2(v1.12)  
{{</mermaid>}}

### 세번째(v1.12 -> v1.13)

{{<mermaid>}}  
graph LR;  
v2(v1.12) --> v3(v1.13)  
{{</mermaid>}}

### 네번째(v1.13 -> v1.14)

{{<mermaid>}}  
graph LR;  
v3(v1.13) --> v4(v1.14)  
{{</mermaid>}}

## 업그레이드 방법

### Cloud Services

{{<admonition note Note true>}}  
몇번의 클릭만 하면 된다.  
{{</admonition>}}

### `kubeadm` 사용

```shell
kubeadm upgrade plane
```

```shell
kubeadm upgrade apply
```

### 어려운 방법

{{<admonition note Note true>}}  
수동 업그레이드를 말한다.  
{{</admonition>}}

## `kubeadm` 로 업그레이드 하기

### 전략

{{<admonition success "공통" true>}}  
모든 전략들은 Master Node 부터 한다.  
{{</admonition>}}

{{<admonition note "한번에" true>}}  
Master Node 를 업그레이드 한 후, 한번에 모든 Worker Nodes 들을 업그레이드 한다.  
{{</admonition>}}

{{<admonition note "각각의 Worker Node 개별" true>}}  
Master Node 를 업그레이드 한 후, 한번에 한개의 Worker Node 를 업그레이드 한다.  
{{</admonition>}}

{{<admonition note "Replace Worker Node" true>}}  
Master Node 를 업그레이드 한 후, 하나의 업그레이드 된 Worker Node 를 생성하고 오래된 버전의 Worker Node 와 교체한다.  
{{</admonition>}}

### 업그레이드 프로세스

{{<admonition note "업그레이드 프로세스" true>}}  
공식 [문서](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)를 참고  
{{</admonition>}}

#### Token 확인

```shell
kubeadm token list
```

#### OS 확인

```shell
cat /etc/*release*
```

#### 현재 설치된 버전 확인

```shell
kubectl get nodes
```

#### OS 패키지 매니저를 통해 업그레이드 버전 찾기

<sub>Ubuntu</sub>

```shell
apt update
apt-cache madison kubeadm
# find the latest 1.21 version in the list
# it should look like 1.21.x-00, where x is the latest patch
```

<sub>CentOS</sub>

```shell
yum list --showduplicates kubeadm --disableexcludes=kubernetes
# find the latest 1.21 version in the list
# it should look like 1.21.x-0, where x is the latest patch
```

#### `kubeadm` 업그레이드

##### OS 패키지 매니저 사용

<sub>Ubuntu</sub>

```shell
apt-mark unhold kubeadm && \
apt-get update && apt-get install -y kubeadm=1.21.x-00 && \
apt-mark hold kubeadm
```

<sub>CentOS</sub>

```shell
yum install -y kubeadm-1.21.x-0 --disableexcludes=kubernetes
```

##### `kubeadm` 버전 확인

```shell
kubeadm version
```

##### `kubeadm` 업그레이드 계획

```shell
kubeadm upgrade plan
```

##### `kubeadm` 업그레이드 적용

```shell
# On Master Node
sudo kubeadm upgrade apply v1.21.x

# On Worker Nodes
sudo kubeadm upgrade node
```

#### `drain` Node

```shell
kubectl drain master --ignore-daemonsets
```

#### `kubelet` 와 `kubectl` 업그레이드

##### OS 패키지 매니저 사용

<sub>Ubuntu</sub>

```shell
apt-mark unhold kubelet kubectl && \
apt-get update && apt-get install -y kubelet=1.21.x-00 kubectl=1.21.x-00 && \
apt-mark hold kubelet kubectl
```

<sub>CentOS</sub>

```shell
yum install -y kubelet-1.21.x-0 kubectl-1.21.x-0 --disableexcludes=kubernetes
```

##### `kubelet` 재시작

```shell
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

#### `uncordon` Node

```shell
kubectl uncordon master
```

## 간단한 프로세스

### `drain` Node

```shell
kubectl drain <node-name>
```

### 업그레이드 계획 확인

```shell
kubeadm upgrade plan
```

### `kubeadm` 업그레이드

```shell
apt-get upgrade -y kubeadm=1.12.0-00
```

### `kubeadm` 적용

```shell
# On Master Node
kubeadm upgrade apply v1.12.0

# On Worker Nodes
kubeadm upgrade node
```

### Node 들 확인

```shell
kubectl get nodes
```

### `kubelet` 컴포넌트 업그레이드

```shell
apt-get upgrade -y kubelet=1.12.0-00
```

### `kubelet` 컴포넌트 재시작

```shell
systemctl restart kubelet
```

### `uncordon` Node

```shell
# On Master Node
kubectl uncordon <node-name>
```

## 간단한 다이어그램

{{<mermaid>}}

graph TD;

D(kubectl drain <node-name>) --> C(kubeadm upgrade plan)

C --> U(apt-get upgrade -y kubeadm=1.12.0-00)

U -->|On Master Node| A1(kubeadm upgrade apply v1.12.0)

U -->|On Worker Nodes| A2(kubeadm upgrade node)

A1 --> CN(kubectl get nodes)

A2 --> CN(kubectl get nodes)

CN -->|Upgrade Component| UL(apt-get upgrade -y kubelet=1.12.0-00)

UL -->|Restart Component| R(systemctl restart kubelet)

R -->|On Master Node| UC(kubectl uncordon <node-name>)

{{</mermaid>}}
