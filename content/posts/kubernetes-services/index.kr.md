---
title: "Kubernetes Services"

date: 2021-05-09T14:20:07+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- services

keywords:

- kubernetes
- services

---

## Kubernetes에서 Services란?

Services란, 유저와 프론트엔드, 프론트엔드와 백엔드, 백엔드와 데이터소스간 통신을 위한 방법이다.  
Services는 어플리케이션의 마이크로서비스들의 결합을 낮춰준다.  
Services는 `labels`/`selector` 매칭되는 Node들에 존재하고, 설정된 Port로 Node들간의 접근을 가능하게 한다.  
Service는 노드 안에 존재하고, `labels`/`selector` 매칭되는 POD들 끼리 설정된 Port로 접근 가능하게 한다.

## Services 타입

{{<admonition note NodePort true>}}  
Service는 같은 노드의 POD들끼리 연결을 가능하게 한다.  
{{</admonition>}}

{{<admonition note ClusterIP true>}}  
Service는 클러스터에 가상 IP를 만들어 프론트엔드와 백엔드같이 다른 서비스들 간의 통신을 가능하게 한다.  
여기서 클러스터란, 같은 설정(YAML, Commands, replicas)으로 생성된 POD들의 그룹을 의미한다.  
{{</admonition>}}

{{<admonition note LoadBalancer true>}}  
어플리케이션의 로드벨런싱을 지원한다.  
{{</admonition>}}

## NodePort

{{<admonition note TargetPort true>}}  
Target은 POD에 존재한다.  
TargetPort는 POD의 Port이다.  
POD은 Node에 존재한다.  
Service의 Target Port이다.  
{{</admonition>}}

{{<admonition note Port true>}}  
Service는 Node에 존재한다.  
Target의 Port로 가기위한 Service의 포트이다.  
{{</admonition>}}

{{<admonition note NodePort true>}}  
NodePort는 Node의 Port이다.  
범위는 30000-32767.  
{{</admonition>}}

### YAML

<sub>pod-definition.yml</sub>

```yaml
appVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```

<sub>service-definition.yml</sub>

```yaml
appVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector: # -> This is labels from pod-definition.yml
    app: myapp
    type: front-end
```

### Commands

#### Service 생성

```shell
kubectl create -f service-definition.yml
```

#### Service 확인

```shell
kubectl get services
```

## ClusterIP

### YAML

<sub>pod-definition.yml</sub>

```yaml
appVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```

<sub>service-definition.yml</sub>

```yaml
appVersion: v1
kind: Service
metadata:
  name: back-end
spec:
  type: ClusterIP
  ports:
    - targetPort: 80
      port: 80
  selector: # -> This is labels from pod-definition.yml
    app: myapp
    type: front-end
```

### Commands

#### Service 생성

```shell
kubectl create -f service-definition.yml
```

#### Service 확인

```shell
kubectl get services
```

## LoadBalancer

### YAML

<sub>pod-definition.yml</sub>

```yaml
appVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```

<sub>service-definition.yml</sub>

```yaml
appVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: LoadBalancer # -> When you use Cloud Services as Google, AWS and Azure, that's it!
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector: # -> This is labels from pod-definition.yml
    app: myapp
    type: front-end
```

### Commands

#### Service 생성

```shell
kubectl create -f service-definition.yml
```

#### Service 확인

```shell
kubectl get services
```

## Commands

### Service 확인

```shell
kubectl get services
```

### Service 상세정보

```shell
kubectl describe service myapp-service
```
