---
title: "Kubernetes Namespace"

date: 2021-05-08T16:13:23+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- namespace

keywords:

- kubernetes
- namespace

---

## 알고가자!

쿠버네티스가 인스톨 될 때 `default` 네임스페이스가 생성된다.\
그리고 `kube-system`과 `kube-public` 네임스페이스들이 있다.

## DNS

### 같은 네임스페이스의 서비스에 연결

`default`라는 같은 네임스페이스의 데이터베이스 서비스인 `db-service`에 연결

```javascript
mysql.connect("db-service")
```

### 다른 네임스페이스의 서비스에 연결

`dev`라는 다른 네임스페이스의 데이터베이스 서비스인 `db-service`에 연결

```javascript
mysql.connect("db-service.dev.svc.cluster.local")
```

{{<admonition note "cluster.local" true>}}  
쿠버네티스 클러스터의 기본 도메인  
{{</admonition>}}

{{<admonition note "svc" true>}}  
서비스의 서브 도메인  
{{</admonition>}}

{{<admonition note "dev" true>}}  
네임스페이스 {{</admonition>}}

{{<admonition note "db-service" true>}}  
서비스명  
{{</admonition>}}

### 특정 네임스페이스의 POD 리스트 확인

```shell
# `default` 네임스페이스의 POD 확인
kubectl get pods

# `kube-system` 네임스페이스의 POD 확인
kubectl get pods --namespace=kube-system
```

### 특정 네임스페이스에 POD 생성

<sub>pod-definition.yml</sub>

```yaml
# `default` 네임스페이스에 생성
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```

<sub>dev-pod-definition.yml</sub>

```yaml
# `dev` 네임스페이스에 생성
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  namespace: dev # -> 네임스페이스
  labels:
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```

<sub>Create a POD</sub>

```shell
# `default` 네임스페이스에 생성
kubectl create -f pod-definition.yml

# `dev` 네임스페이스에 생성
kubectl create -f pod-definition.yml --namespace=dev
kubectl create -f dev-pod-definition.yml
```

## 네임스페이스 생성

<sub>namespace-dev.yml</sub>

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

<sub>네임스페이스 생성</sub>

```shell
# With YAML file
kubectl create -f namesapce-dev.yml

# Command
kubectl create namesapce dev
```

## 현재 터미널의 네임스페이스 변경

```shell
kubectl config set-context $(kubectl config current-context) --namespace=dev

# `dev` 네임스페이스의 POD들을 `--namespace` 옵션 없이 확인할 수 있다.
kubectl pods
```

## 모든 네임스페이스의 POD 확인

```shell
kubectl get pods --all-namespaces
```

## Resource Quota

<sub>compute-quota.yml</sub>

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```

<sub>Quota 생성</sub>

```shell
kubectl create -f compute-quota.yml
```

