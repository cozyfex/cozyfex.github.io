---
title: "Kubernetes Authentication"

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
All accounts is managed by `kube-apiserver`.  
{{</admonition>}}

| Service | Type | For | Intended To |  
|:-:|:-:|:-:|:-:|  
| Application | End Users | Humans | Application |   
| Kubernetes | Admins | Humans | Global(Names must be unique across all namespaces of a cluster) |   
| Kubernetes | Developers | Humans | Global(Names must be unique across all namespaces of a cluster) |   
| Kubernetes | Service Accounts | Processes Run in PODs | Namespace(Names must be unique in a namespace) |

### Admins and Developers

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

## Auth Mechanisms

### Mechanisms

| Type | Description |  
|:-:|:-:|  
| Static Password File | File in Kubernetes |  
| Static Token File | File in Kubernetes |  
| Certificates | External Certifications |  
| Identity Services | External Services |

### Managed by `kube-apiserver`

{{<mermaid>}}

graph TD;

API(kube-apiserver) --> SP(Static Password File)

API --> ST(Static Token File)

API --> C(Certificates)

API --> IS(Identity Services)

{{</mermaid>}}

### Auth Mechanisms Basic

{{<admonition note Note true>}}  
This is not a recommended authentication mechanism.  
Consider volume mount while providing the auth file in a `kubeadm` setup.  
Setup Role Based Authorization for the new users.  
{{</admonition>}}

#### Static Password File

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

##### Password Authenticate User

```shell
curl -v -k https://master-node-ip:6443/api/v1/pods -u "user1:password123"
```

#### Static Token File

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

##### Token Authenticate User

```shell
curl -v -k https://master-node-ip:6443/api/v1/pods --header "Authorization: Bearer KpjCVbI7rCFAHYPkByTIzRb7gu1cUc4B"
```
