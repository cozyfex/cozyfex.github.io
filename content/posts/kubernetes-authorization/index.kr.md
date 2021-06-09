---
title: "Kubernetes Authorization"

date: 2021-05-24T16:31:00+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- authorization

keywords:

- kubernetes
- security
- authorization

---

## 왜 권한부여를 하나?

### Admins

#### PODs

```shell
kubectl get pods
```

```shell
NAME      STATUS  ROLES   AGE     VERSION
worker-1  Ready   <none>  5d21h   v1.13.0
worker-2  Ready   <none>  5d21h   v1.13.0
```

#### Nodes

```shell
kubectl get nodes
```

```shell
NAME      STATUS  ROLES   AGE     VERSION
worker-1  Ready   <none>  5d21h   v1.13.0
worker-2  Ready   <none>  5d21h   v1.13.0
```

#### Node 삭제

```shell
kubectl delete node worker-2
```

```shell
Node worker-2 Deleted!
```

### Developers

#### PODs

```shell
kubectl get pods
```

```shell
NAME      STATUS  ROLES   AGE     VERSION
worker-1  Ready   <none>  5d21h   v1.13.0
worker-2  Ready   <none>  5d21h   v1.13.0
```

#### Nodes

```shell
kubectl get nodes
```

```shell
NAME      STATUS  ROLES   AGE     VERSION
worker-1  Ready   <none>  5d21h   v1.13.0
worker-2  Ready   <none>  5d21h   v1.13.0
```

#### Node 삭제

```shell
kubectl delete node worker-2
```

```shell
Error from server (Forbidden): nodes
"worker-1" is forbidden: User "developer"
cannot delete resource "nodes"
```

### Bots

#### PODs

```shell
kubectl get pods
```

```shell
Error from server (Forbidden): nodes
"worker-1" is forbidden: User "Bot-1"
delete resource "nodes"
```

#### Nodes

```shell
kubectl get nodes
```

```shell
Error from server (Forbidden): nodes
"worker-1" is forbidden: User "Bot-1"
delete resource "nodes"
```

#### Node 삭제

```shell
kubectl delete node worker-2
```

```shell
Error from server (Forbidden): nodes
"worker-1" is forbidden: User "Bot-1"
delete resource "nodes"
```

## 권한부여 메커니즘

### Node 권한부여

{{<admonition note "Node Authorization" true>}}  
Node 권한부여자는 kubelet 이 API 작업을 실행하는것을 허용해준다.  
공식 문서 [Node Authorization](https://kubernetes.io/docs/reference/access-authn-authz/node/)  
{{</admonition>}}

#### 읽기 작업

- services
- endpoints
- nodes
- pods
- secrets, configmaps, persistent volume claims and persistent volumes related to pods bound to the kubelet's node

#### 쓰기 작업

- nodes and node status
- pods and pod status
- events

#### 인증 관련 작업

### ABAC 권한부여

{{<admonition note "ABAC 권한부여" true>}}  
ABAC(Attribute Based Access Control - 속성 기반 접근 제어)는 속성을 함께 결합하는 정책을 사용하여 접근 권한을 부여하는 접근 제어 패러다임으로 정의된다.  
공식문서 [ABAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/abac/)  
{{</admonition>}}

### RBAC 권한부여

{{<admonition note "RBAC 권한부여" true>}}  
RBAC(Role Based Access Control - 역활 기반 접근 제어)는 해당 조직에서 역할을 기반으로 컴퓨터나 네트워크에 접근을 조절하는 방법이다.  
RBAC 권한부여는 권한부여 결정과 Kubernetes API 를 통해 동적 설정 정책을 허용할때 rbac.authorization.k8s.io API 그룹을 사용한다.  
RBAC 를 허용하기 위해서, API 서버를 실행 할 때 --authorization-mode 옵션을 RBAC 가 포함된 콤마로 구분한 목록과 함께 설정한다.

예제

```shell
kube-apiserver --authorization-mode=Example,RBAC --other-options --more-options
```

The official document is [RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)  
{{</admonition>}}

### Webhook Mode

{{<admonition note "Webhook" true>}}  
WebHook 은 HTTP 콜백이다.  
어떤 엑션이 발생했을 때, HTTP POST 가 발생한다.  
HTTP POST 를 통한 간한단 이벤트 알림이다.  
웹 어플리케이션은 확실한 이벤트가 발생했을때 웹훅이 메시지를 URL 로 POST 하면 구현한다.  
공식문서 [Webhook](https://kubernetes.io/docs/reference/access-authn-authz/webhook/)
{{</admonition>}}

## Authorization Mode

{{<mermaid>}}  
graph TD;  
AA(AlwaysAllow)  
N(NODE)  
A(ABAC)  
R(RBAC)  
W(WEBHOOK)  
AD(AlwaysDeny)  
{{</mermaid>}}

### Setting in Kube API

{{<admonition note "--authorization-mode" true>}}  
`--authorization-mode`=Node,RBAC,Webhook  
{{</admonition>}}

{{<mermaid>}}  
stateDiagram  
User --> NODE  
NODE --> RBAC  
NODE --> User  
RBAC --> User  
RBAC --> WEBHOOK  
WEBHOOK --> User  
{{</mermaid>}}

```shell
vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

```shell
...
...

spec:
  containers:
  - command:
    - kube-apiserver
    - --advertise-address=192.168.49.2
    - --allow-privileged=true
    - --authorization-mode=Node,RBAC,Webhook
    - --client-ca-file=/var/lib/minikube/certs/ca.crt
    - --enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,DefaultTolerationSeconds,NodeRestriction,MutatingAdmissionWebhook,ValidatingAdmissionWebhook,ResourceQuota
    - --enable-bootstrap-token-auth=true
    - --etcd-cafile=/var/lib/minikube/certs/etcd/ca.crt
    - --etcd-certfile=/var/lib/minikube/certs/apiserver-etcd-client.crt
    - --etcd-keyfile=/var/lib/minikube/certs/apiserver-etcd-client.key
    - --etcd-servers=https://127.0.0.1:2379
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

...
...
```