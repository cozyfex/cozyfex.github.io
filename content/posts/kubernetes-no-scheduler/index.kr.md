---
title: "Kubernetes 스케줄러가 없다!"

date: 2021-05-10T13:15:44+09:00

author: CozyFex

categories:

- kubernetes
- kubernetes scheduler
- k8s
- orchestration

tags:

- kubernetes
- scheduler
- nodename

keywords:

- kubernetes
- scheduler
- nodename

---

## 알고가자!

{{<admonition note "Note" true>}}  
그냥 스케줄러가 실행중이도록 하는게 좋아보인다!
{{</admonition>}}

## 스케줄러가 없다!

{{<admonition note "무슨일이 일어난거야?" true>}}  
스케줄러가 실행중이 아니고, `POD`이 생성될 때 `nodeName`이 정의되어 있지 않으면, `POD`의 상태가 Pending 이 된다.  
{{</admonition>}}

## 어떻게 상태를 Pending 에서 Running 으로 변경할까?

### 스케줄러가 없을때의 동작

#### POD YAML

<sub>pod-definition.yml</sub>

```yaml
appVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 8080
```

#### POD 생성

```shell
kubectl apply -f pod-definition.yml
```

#### 상태 체크

```shell
kubectl get pods
```

```
NAME    READY   STATUS      RESTARTS    AGE
nginx   0/1     Pending     0           3s
```

엇! 상태가 Pending 이다!!

### 고쳐보자!

#### POD YAML with `nodeName`

<sub>pod-nodename-definition.yml</sub>

```yaml
appVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 8080
  nodeName: node02 # -> 이 값을 설정해야만 Pending 에서 Running 으로 변경 할 수 있다.
```

#### POD 생성

```shell
kubectl apply -f pod-nodename-definition.yml
```

#### 상태 체크

```shell
kubectl get pods
```

```
NAME    READY   STATUS      RESTARTS    AGE     IP          NODE
nginx   1/1     Running     0           9s      10.40.0.4   node02
```

와우, Running 이다!