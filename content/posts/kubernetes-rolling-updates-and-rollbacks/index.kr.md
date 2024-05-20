---
title: "Kubernetes Rolling Updates and Rollbacks"

date: 2021-05-16T20:57:50+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- application lifecycle management
- update
- rolling
- rolling update
- rollback

keywords:

- kubernetes
- application lifecycle management
- update
- rolling
- rolling update
- rollback

---

## Rollout and Versioning

{{<admonition note "Version 이란?" true>}}  
컨테이너 `image` 의 버전을 의미한다.  
예로, nginx:1.7.0 를 nginx:1.7.1 로 변경한다.  
{{</admonition>}}

### Rollout 명령

#### Rollout 상태

```shell
kubectl rollout status deployment/myapp-deployment
```

#### Rollout 기록

```shell
kubectl rollout history deployment/myapp-deployment
```

## 배포 전략

{{<admonition note "가정" true>}}  
nginx:1.7.0 를 nginx:1.7.1 로 업그레이드 해야 한다고 가정하자.  
{{</admonition>}}

### Recreate

{{<admonition note Recreate true>}}  
모든 어플리케이션을 지우고 생성하는것을 한번에 한다.  
그래서, 이 시간동안에는 사용자들은 어플리케이션에 접근하지 못한다.  
{{</admonition>}}

### Rolling Update

{{<admonition note "Rolling Update" true>}}  
한번에 소수 몇개의 `POD`을 지우고 생성한다.  
사용자는 항상 어플리케이션에 접근이 가능하다.  
{{</admonition>}}

### 대상 Deployment 의 전략 확인

{{<admonition note StrategyType true>}}  
Deployment 의 상세정보에서 `StrategyType` 를 확인하면 된다.  
{{</admonition>}}

```shell
kubectl describe deployment myapp-deployment | grep -i strategy
```

## 업그레이드 적용

```shell
# Declarative - After edit YAML
kubectl apply -f deployment-definition.yaml

# Imperative
kubectl set image deployment/myapp-deployment nginx-container=nginx:1.7.1
```

## Rollback

### Command

```shell
kubectl rollout undo deployment/myapp-deployment
```

### 업그레이드 상태 확인

```shell
kubectl get replicasets
```

```
# Before rollback
NAME                            DESIRED     CURRENT     READY   AGE 
myapp-deployment-67c749c58c     0           0           0       22m 
myapp-deployment-7d57dbdb8d     5           5           5       20m

# After rollback
NAME                            DESIRED     CURRENT     READY   AGE 
myapp-deployment-67c749c58c     5           5           5       22m 
myapp-deployment-7d57dbdb8d     0           0           0       20m
```