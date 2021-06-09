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

## Why Authorization?

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

#### Delete Node

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

#### Delete Node

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

#### Delete Node

```shell
kubectl delete node worker-2
```

```shell
Error from server (Forbidden): nodes
"worker-1" is forbidden: User "Bot-1"
delete resource "nodes"
```

## Authorization Mechanisms

### Node Authorization

{{<admonition note "Node Authorization" true>}}  
The Node authorizer allows a kubelet to perform API operations.  
The official document is [Node Authorization](https://kubernetes.io/docs/reference/access-authn-authz/node/)  
{{</admonition>}}

#### Read Operations

- services
- endpoints
- nodes
- pods
- secrets, configmaps, persistent volume claims and persistent volumes related to pods bound to the kubelet's node

#### Write Operations

- nodes and node status
- pods and pod status
- events

#### Auth-related Operation

### ABAC Authorization

{{<admonition note "ABAC Authorization" true>}}  
Attribute-based access control (ABAC) defines an access control paradigm whereby access rights are granted to users through the use of policies which combine attributes together.  
The official document is [ABAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/abac/)  
{{</admonition>}}

### RBAC Authorization

{{<admonition note "RBAC Authorization" true>}}  
Role-based access control (RBAC) is a method of regulating access to computer or network resources based on the roles of individual users within your organization.  
RBAC authorization uses the rbac.authorization.k8s.io API group to drive authorization decisions, allowing you to dynamically configure policies through the Kubernetes API.  
To enable RBAC, start the API server with the --authorization-mode flag set to a comma-separated list that includes RBAC; for example:

```shell
kube-apiserver --authorization-mode=Example,RBAC --other-options --more-options
```

The official document is [RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)  
{{</admonition>}}

### Webhook Mode

{{<admonition note "Webhook" true>}}  
A WebHook is an HTTP callback: an HTTP POST that occurs when something happens; a simple event-notification via HTTP POST. A web application implementing WebHooks will POST a message to a URL when certain things happen.  
When specified, mode Webhook causes Kubernetes to query an outside REST service when determining user privileges.  
The official document is [Webhook](https://kubernetes.io/docs/reference/access-authn-authz/webhook/)  
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