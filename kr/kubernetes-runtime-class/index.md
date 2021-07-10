# Kubernetes Runtime Class


## Container Runtime Handlers

| Runtime | Handler |  
|:-:|:-:|  
| gVisor | runsc |  
| Kata | kata |

## gVisor

### `gvisor.yaml`

```yaml
apiVersion: node.k8s.io/v1beta1
kind: RuntimeClass
metadata:
  name: gvisor
handler: runsc
```

### RuntimeClass 생성

```shell
kubectl create -f gvisor.yaml
```

## Kata

### `kata.yaml`

```yaml
apiVersion: node.k8s.io/v1beta1
kind: RuntimeClass
metadata:
  name: kata
handler: kata
```

### RuntimeClass 생성

```shell
kubectl create -f gvisor.yaml
```

## POD 에 적용

### gVisor POD

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  runtimeClassName: gvisor
  containers:
    - image: nginx
      name: nginx
```

### Kata POD

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  runtimeClassName: kata
  containers:
    - image: nginx
      name: nginx
```

## Container Runtime 확인

### 해당 Node 에 접속

```shell
ssh node01
```

### Grep Process

```shell
pgrep -a nginx
```

```shell
pgrep -a runsc
```

```shell
pgrep -a kata
```


