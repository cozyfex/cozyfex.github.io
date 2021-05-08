---
title: "Kubernetes POD"

date: 2021-05-07T12:54:42+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- k8s
- pod

keywords:

- kubernetes
- k8s
- pod

---

### 알고가자!

`POD`은 서비스 단위로 이해하는게 좋다.\
쿠버네티스는 스테일 아웃을 할 때 `POD` 단위로 복제한다.\
`POD` 내부에 컨테이너를 개별로 스케일 아웃 하지 않는다.

### 도커로 컨테이너 관리하기

```shell
# app1
docker run python-app
# app2
docker run python-app
# app3
docker run python-app
# app4
docker run python-app

docker run helper -link app1
docker run helper -link app2
docker run helper -link app3
docker run helper -link app4
```

### POD으로 컨테이너 관리하기

#### pod-definition.yml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-continer
      image: nginx
```

#### YAML파일로 POD 생성하기

```shell
kubectl create -f pod-definition.yml
```

#### 명령으로 POD 생성하기

```shell
kubectl run myapp-pod --image nginx
```

#### POD 확인

```shell
kubectl get pods
```

#### POD 상세정보

```shell
kubectl describe pod myapp-pod
```
