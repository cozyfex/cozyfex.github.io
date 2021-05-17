---
title: "Kubernetes initContainers"

date: 2021-05-17T01:59:59+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- application lifecycle management
- initcontainers

keywords:

- kubernetes
- application lifecycle management
- initcontainers

---

## initContainers 란?

{{<admonition note initContainers true>}}  
`initContainers` 는 `POD` 이 시작 할 때 실행되는 것이다.  
`initContainers` 는 정의된 순서대로 실행된다.  
`initContainers` 들 중, 하나라도 실패하면 쿠버네티스는 `POD` 을 성공할 때 까지 재시작 한다.  
{{</admonition>}}

## initContainers YAML

<sub>pod-definition.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
    - name: myapp-container
      image: nginx
      command: [ "echo", "This is main conatiner." ]
  initContainers:
    - name: init-myservice
      image: nginx
      command: [ "echo", "This is one of initContainers. Init Service." ]
    - name: init-mydb
      image: mysql
      command: [ "echo", "This is one of initContainers. Init Database." ]
```