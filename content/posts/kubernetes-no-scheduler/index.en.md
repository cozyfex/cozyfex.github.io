---
title: "Kubernetes No Scheduler"

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

## You must know!

{{<admonition note "Note" true>}}  
I think you'd better keep running scheduler.  
{{</admonition>}}

## No Scheduler!

{{<admonition note "What happened?" true>}}  
When the scheduler is not running and the `nodeName` is undefined when the `POD` is created, the `POD`'s status is in
Pending.  
{{</admonition>}}

## How to change does the status Pending to Running

### No Scheduler Simulation

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

#### Create a POD

```shell
kubectl apply -f pod-definition.yml
```

#### Check Status

```shell
kubectl get pods
```

```
NAME    READY   STATUS      RESTARTS    AGE
nginx   0/1     Pending     0           3s
```

Oops!! It's Pending status!!

### How to fix it!

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
  nodeName: node02 # -> You have to set this value when you need to change the status Pending to Running.
```

#### Create a POD

```shell
kubectl apply -f pod-nodename-definition.yml
```

#### Check Status

```shell
kubectl get pods
```

```
NAME    READY   STATUS      RESTARTS    AGE     IP          NODE
nginx   1/1     Running     0           9s      10.40.0.4   node02
```

Wow, it's Running now!