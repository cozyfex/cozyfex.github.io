---
title: "Kubernetes 인증"

date: 2021-05-19T13:09:32+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- authentication

keywords:

- kubernetes
- security
- authentication

---

## Accounts

{{<admonition note Accounts true>}}  
모든 계정은 `kube-apiserver` 이 관리한다.  
{{</admonition>}}

| Service | Type | For | Intended To |  
|:-:|:-:|:-:|:-:|  
| 어플리케이션 | 어플리케이션 사용자 | 사람 | 어플리케이션 |   
| 쿠버네티스 | 관리자 | 사람 | Global(클러스터의 모든 네임스페이스에서 유니크 해야만 한다) |   
| 쿠버네티스 | 개발자 | 사람 | Global(클러스터의 모든 네임스페이스에서 유니크 해야만 한다) |   
| 쿠버네티스 | Service Accounts | POD 에서 돌아가는 프로세스들 | Namespace(해당 네임스페이스에서 유니크 해야만 한다) |

### 관리자와 개발자

<sub>Create a user</sub>

```shell
kubectl create user user1
```

<sub>User list</sub>

```shell
kubectl list users
```

### Service Accounts

<sub>Create a service account</sub>

```shell
kubectl create serviceAccount sa1
```

<sub>Service accounts list</sub>

```shell
kubectl list serviceAccount
```

### Process

{{<mermaid>}}

graph TD;

A(Admins) --> KB[kubectl]

D(Developers) --> EP[curl https://kube-server-ip:6443]

KB --> API(kube-apiserver)

EP --> API

API --> AU[Authenticate User]

AU --> P[Process Request]

{{</mermaid>}}

## 인증 메커니즘

### 메커니즘

| Type | Description |  
|:-:|:-:|  
| 정적 암호 파일 | 쿠버네티스 파일 |  
| 정적 토큰 파일 | 쿠버네티스 파일 |  
| 인증서 | 외부 인증서 |  
| 신원확인 서비스 | 외부 서비스 |

### `kube-apiserver` 관리

{{<mermaid>}}

graph TD;

API(kube-apiserver) --> SP(정적 암호 파일)

API --> ST(정적 토큰 파일)

API --> C(Certificates)

API --> IS(Identity Services)

{{</mermaid>}}

### 기본 인증 메커니즘

{{<admonition note Note true>}}  
이 인증 메커니즘은 추천되지 않는다.  
`kubeadm` 을 설치 할 때 볼륨을 마운트 해서 제공하는것을 고려하라.  
새 사용자에 대한 역할 기반 권한을 설정합니다.  
{{</admonition>}}

#### 정적 암호 파일

##### `user-details.csv`

```
password123,user1,u0001,group1
password123,user2,u0002,group1
password123,user3,u0003,group2
password123,user4,u0004,group2
password123,user5,u0005,group2
```

##### `kube-apiserver.service`

<sub>`basic-auth-file`</sub>

```shell
ExecStart=/usr/local/bin/kube-apiserver \\
    --advertise-address=${INTERNAL_IP} \\
    --allow-privileged=true \\
    --apiserver-count=3 \\
    --authorization-mode=Node,RBAC \\
    --bind-address=0.0.0.0 \\
    --enable-swagger-ui=true \\
    --etcd-servers=https://127.0.0.1:2379 \\
    --event-ttl=1h \\
    --runtime-config=api/all \\
    --service-cluster-ip-range=10.32.0.0/24 \\
    --service-node-port-range=30000-32767 \\
    --v=2
    --basic-auth-file=user-details.csv
```

##### 암호 사용자 인증

```shell
curl -v -k https://master-node-ip:6443/api/v1/pods -u "user1:password123"
```

#### 정적 토큰 파일

##### `user-token-details.csv`

```
KpjCVbI7rCFAHYPkByTIzRb7gu1cUc4B,user10,u0010,group1
rJjncHmvtXHc6MlWQddhtvNyyhgTdxSC,user11,u0011,group1
mjpOFIEiFOkL9toikaRNtt59ePtczZSq,user12,u0012,group2
PG41IXhs7QjqwWkmBkvgGT9glOyUqZij,user13,u0013,group2
```

##### `kube-apiserver.service`

<sub>`--token-auth-file`</sub>

```shell
ExecStart=/usr/local/bin/kube-apiserver \\
    --advertise-address=${INTERNAL_IP} \\
    --allow-privileged=true \\
    --apiserver-count=3 \\
    --authorization-mode=Node,RBAC \\
    --bind-address=0.0.0.0 \\
    --enable-swagger-ui=true \\
    --etcd-servers=https://127.0.0.1:2379 \\
    --event-ttl=1h \\
    --runtime-config=api/all \\
    --service-cluster-ip-range=10.32.0.0/24 \\
    --service-node-port-range=30000-32767 \\
    --v=2
    --token-auth-file=user-token-details.csv
```

##### 토큰 사용자 인증

```shell
curl -v -k https://master-node-ip:6443/api/v1/pods --header "Authorization: Bearer KpjCVbI7rCFAHYPkByTIzRb7gu1cUc4B"
```
