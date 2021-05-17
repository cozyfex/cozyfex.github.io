---
title: "Kubernetes Application Logs"

date: 2021-05-16T19:43:26+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- logs

keywords:

- kubernetes
- logs

---

## Docker Logs

### Run Docker

[Docker Run Command](https://docs.docker.com/engine/reference/commandline/run/)

```shell
docker run <image>

# Run with `-d` option that run container in background and print container ID.
docker run -d <image>
```

### Container Logs

[Docker Logs Command](https://docs.docker.com/engine/reference/commandline/logs/)

```shell
docker logs -f <container-name>
```

## Kubernetes Logs

### Create PODs

<sub>event-simulator.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: event-simulator-pod
spec:
  containers:
    - name: event-simulator
      image: kodekloud/event-simulator
```

<sub>multiple-events-simulator.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multiple-events-simulator-pod
spec:
  containers:
    - name: event-simulator
      image: kodekloud/event-simulator
    - name: another-simulator
      image: kodekloud/event-simulator
```

<sub>Command</sub>

```shell
kubectl create –f event-simulator.yaml
kubectl create –f multiple-events-simulator.yaml
```

### Single Container POD

```shell
# kubectl logs –f <pod-name>

kubectl logs –f event-simulator-pod
```

### Multiple Container POD

```shell
# kubectl logs –f <pod-name> <container-name>

kubectl logs –f multiple-events-simulator-pod event-simulator
```

