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

{{<admonition note "What's the Version?" true>}}  
The Version is a `image` version of the container.  
For example, nginx:1.7.0 to nginx:1.7.1  
{{</admonition>}}

### Rollout Commands

#### Rollout Status

```shell
kubectl rollout status deployment/myapp-deployment
```

#### Rollout History

```shell
kubectl rollout history deployment/myapp-deployment
```

## Deployment Strategy

{{<admonition note "Let's assume" true>}}  
You need to upgrade nginx:1.7.0 to nginx:1.7.1 now.  
{{</admonition>}}

### Recreate

{{<admonition note Recreate true>}}  
Delete all application and create all application at a time.  
So, during the time, users cannot access the application.  
{{</admonition>}}

### Rolling Update

{{<admonition note "Rolling Update" true>}}  
Delete a few `POD`s and create a few `POD`s at a time.  
Then users can access the application even if it's in the maintenance time.  
{{</admonition>}}

### Check Strategy of the Deployment

{{<admonition note StrategyType true>}}  
You just need to check `StrategyType` in the description of the deployment.  
{{</admonition>}}

```shell
kubectl describe deployment myapp-deployment | grep -i strategy
```

## Apply Upgrade

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

### Check Upgrade Status

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