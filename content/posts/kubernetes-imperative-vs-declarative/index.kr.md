---
title: "Kubernetes 명령적 vs 선언적"

date: 2021-05-09T15:51:31+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- object
- imperative
- declarative

keywords:

- kubernetes
- object
- imperative
- declarative

---

## 쿠버네티스가 오브젝트를 관리하는 두가지 방법

{{<admonition note "명령적" true>}}  
명령으로 관리  
항상 현재 상태를 알고 있어야 한다.  
{{</admonition>}}

{{<admonition note "선언적" true>}}  
파일로 관리  
{{</admonition>}}

## 명령적

### Commands

#### Objects 생성

```shell
# Create a POD and Run
kubectl run --image=nginx nginx

# Create a Deployment
kubectl create deployment --image nginx nginx

# Expose and Create a Deployment
kubectl expose deployment nginx --port=80
```

#### Objects 변경

```shell
# Edit Deployment
kubectl edit deployment nginx

# Scale Deployment
kubectl scale deployment nginx --replicas=5

# Update Image Deployment
kubectl set image deployment nginx nginx=nginx:1.18
```

#### Commands with YAML

```shell
# Create with YAML
kubectl create -f nginx.yml

# Replace with YAML
kubectl replace --force -f nginx.yml

# Delete with YAML
kubectl delete -f nginx.yml
```

## 선언적

### Commands

{{<admonition note "kubectl apply" true>}}  
`apply`는 자동으로 오브젝트가 존재하는지 알아낸다.  
{{</admonition>}}

```shell
# Apply a file
kubectl apply -f nginx.yml

# Apply files
kubectl apply -f /path/to/files
```

## 공통 명령 옵션

### --dry-run

{{<admonition note "--dry-run" true>}}  
`--dry-run` is for testing your command.  
`--dry-run=client` is to execute the command without create objects.  
{{</admonition>}}

### -o yaml

{{<admonition note "-o yaml" true>}}  
`-o yaml` will output the resource definition in YAML format on the screen.  
{{</admonition>}}

## 명령 예제

### POD

```shell
# Create a NGINX POD
kubectl run nginx --image=nginx

# Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
kubectl run nginx --image=nginx  --dry-run=client -o yaml
```

### Deployment

```shell
# Create a Deployment
kubectl create deployment --image=nginx nginx

# Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml

# Generate Deployment with 4 Replicas
kubectl create deployment nginx --image=nginx --replicas=4

# You can also scale a deployment using the kubectl scale command.
kubectl scale deployment nginx --replicas=4

# Another way to do this is to save the YAML definition to a file.
kubectl create deployment nginx --image=nginx--dry-run=client -o yaml > nginx-deployment.yaml
```

### Service

```shell
# Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379
kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
# (자동으로 POD의 labels를 selector로 사용한다.)

# Or
kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml
# (POD의 labels를 selector로 사용하지 않고, app=redis를 selector로 사용한다. 옵션으로 selector를 전달 할 수 없다. 그래서 다른 POD labels를 가지고 있으면 잘 동작하지 않는다.)

# Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:
kubectl expose pod nginx --port=80 --name nginx-service --type=NodePort --dry-run=client -o yaml
# (자동으로 POD의 labels를 selector로 사용한다. 그러나 특정한 Node Port를 설정할 수는 없다.)

# Or
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml
# (POD의 labels를 selector로 사용하지 않는다.)
```

{{<admonition note "Note" true>}} Both the above commands have their own challenges. While one of it cannot accept a 위의
두 명령은 각자 단점이 존재한다. 하나는 `selector`를, 다른 하나는 Node Port를 지정하지 못한다. 난 `kubectl expose`를 추천한다. 만약, 특정 Node Port와 `selector`를
사용해야 한다면 정의 파일을 만들어서 사용해야 한다.  
{{</admonition>}}