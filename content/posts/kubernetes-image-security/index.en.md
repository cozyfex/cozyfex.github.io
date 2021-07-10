---
title: "Kubernetes Image Security"

date: 2021-05-24T21:48:08+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- image

keywords:

- kubernetes
- security
- image

---

## Image

<sub>nginx-pod.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx
```

{{<admonition note image true>}}  
`image: nginx`  
{{</admonition>}}

## Image Value Structure

{{<admonition note "Structure" true>}}  
`image`: `<repository>`/`<user/account>`/`<image/repository>`  
{{</admonition>}}

<sub>Example</sub>

```yaml
  image: gcr.io/kubernetes-e2e-test-images/dnsutils  
```

## Private Repository

### Login

```shell
docker login private-repository.io
```

```shell
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: registry-user
Password:
WARNING! Your password will be stored unencrypted in /home/vagrant/.docker/config.json.
Login Succeeded
```

### Run Private Image

```shell
docker run private-repository.io/apps/internal-app
```

## Private Repository in Kubernetes

### Create Private Repository Secret in Kubernetes

```shell
kubectl create secret docker-registry regcred \
  --docker-server=private-repository.io \
  --docker-username=registry-user \
  --docker-password=registry-password \
  --docker-email=registry-user@org.com
```

### Private Repository in YAML

<sub>nginx-pod.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  imagePullSecrets:
    - name: regcred
  containers:
    - name: nginx
      image: private-repository.io/apps/internal-app
```