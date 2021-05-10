---
title: "Kubernetes 스케줄러 바인드 nodeName"

date: 2021-05-10T12:31:32+09:00

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

## 스케줄러는 어떻게 작동하나?

{{<admonition note "Scheduler" true>}}  
스케줄러가 동작중일때는, 스케줄러가 `nodeName`이 정의되지 않은 새로운 `POD`에 Node를 바인딩한다.  
{{</admonition>}}

## POD YAML

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
# nodeName: node02 # -> 이 값은 기본값으로 설정할 필요는 없다.
```

## POD 생성

```shell
kubectl create -f pod-definition.yml
```

## 스케줄러 프로세스

{{<mermaid>}}  
graph TD;  
C(kubectl create) --> R(pod-definition.yml 읽기)  
R --> F(<nodeName> 찾기)  
F --> N(<nodeName> 없음)  
N --> B(<nodeName> 자동 바인딩)  
B --> P(POD을 Node에 바인딩)  
{{</mermaid>}}
