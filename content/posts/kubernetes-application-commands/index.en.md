---
title: "Kubernetes Application Commands"

date: 2021-05-16T22:32:31+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- application lifecycle management
- application commands

keywords:

- kubernetes
- application lifecycle management
- application commands

---

## Docker

### Command

```shell
docker run ubuntu sleep 5
```

### Dockerfile

```dockerfile
FROM ubuntu:latest

# Correct
CMD sleep 5
CMD ["sleep", "5"]

# Incorrect
# CMD ["sleep 5"]
```

### Dockerfile and Command

#### CMD

```dockerfile
FROM ubuntu:latest

CMD sleep 5
```

```shell
docker build -t ubunut-cmd-sleeper .
```

```shell
# sleep 5
docker run ubuntu-cmd-sleeper

# sleep 10
docker run ubuntu-cmd-sleeper sleep 10
```

#### ENTRYPOINT

```dockerfile
FROM ubuntu:latest

ENTRYPOINT ["sleep"]
```

```shell
docker build -t ubuntu-entrypoint-sleeper .
```

```shell
# Error
docker run ubuntu-entrypoint-sleeper
# sleep: missing operand
# Try 'sleep --help' for more information.
```

```shell
# sleep 20
docker run ubunut-entrypoint-sleeper 20
```

#### ENTRYPOINT with Default Argument

```dockerfile
FROM ubuntu:latest

ENTRYPOINT ["sleep"]

CMD ["3"]
```

```shell
docker build -t ubuntu-entrypoint-cmd-sleeper .
```

```shell
# sleep 3
docker run ubuntu-entrypoint-cmd-sleeper

# sleep 15
docker run ubuntu-entrypoint-cmd-sleeper 15

# cat /etc/passwd
docker run --entrypoint cat ubuntu-entrypoint-cmd-sleeper /etc/passwd
```

## Kubernetes

### Base Docker Image

```dockerfile
FROM ubuntu:latest

ENTRYPOINT ["sleep"]

CMD ["3"]
```

```shell
docker build -t ubuntu-sleeper .
```

### Docker Command to YAML

```shell
docker run --name sleeper-container ubuntu-sleeper
```

<sub>ubuntu-sleeper.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  restartPolicy: Never
  containers:
    - name: sleeper-container
      image: ubunut-sleeper
      imagePullPolicy: Never
```

```shell
kubectl create -f ubuntu-sleeper.yaml
```

### Docker Command to YAML with Argument Replacement

```shell
# sleep 3
docker run --name sleeper-container ubuntu-sleeper 50
```

<sub>ubuntu-sleeper-argument.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  restartPolicy: Never
  containers:
    - name: sleeper-container
      image: ubunut-sleeper
      imagePullPolicy: Never
      args: [ "50" ]
```

```shell
# sleep 50
kubectl create -f ubuntu-sleeper-argument.yaml
```

### Docker Command to YAML with Command and Argument Replacement

```shell
docker run --name sleeper-container --entrypoint cat ubuntu-sleeper /etc/passwd
```

<sub>ubuntu-sleeper-command-argument.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  restartPolicy: Never
  containers:
    - name: sleeper-container
      image: ubunut-sleeper
      imagePullPolicy: Never
      command: [ "cat" ]
      args: [ "/etc/passwd" ]
```

Or

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  restartPolicy: Never
  containers:
    - name: sleeper-container
      image: ubunut-sleeper
      imagePullPolicy: Never
      command:
        - "cat"
        - "/etc/passwd"
```

Or

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  restartPolicy: Never
  containers:
    - name: sleeper-container
      image: ubunut-sleeper
      imagePullPolicy: Never
      command: [ "cat", "/etc/passwd" ]
```

```shell
# cat /etc/passwd
kubectl create -f ubuntu-sleeper-command-argument.yaml
```
