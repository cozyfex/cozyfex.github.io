---
title: "Kubernetes Labels and Selector"

date: 2021-05-10T14:26:36+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- labels
- selector

keywords:

- kubernetes
- labels
- selector

---

## Labels

{{<admonition note "POD A-frt" true>}}  
| Key | Value |  
|:-----:|:--------:|  
| app | frontend |  
| color | red |  
| shape | triangle |  
{{</admonition>}}

{{<admonition note "POD B-fyt" true>}}  
| Key | Value |  
|:-----:|:--------:|  
| app | frontend |  
| color | yellow |  
| shape | triangle |  
{{</admonition>}}

{{<admonition note "POD C-fgt" true>}}  
| Key | Value |  
|:-----:|:--------:|  
| app | frontend |  
| color | green |  
| shape | triangle |  
{{</admonition>}}

{{<admonition note "POD D-brt" true>}}  
| Key | Value |  
|:-----:|:--------:|  
| app | backend |  
| color | red |  
| shape | triangle |  
{{</admonition>}}

{{<admonition note "POD E-byt" true>}}  
| Key | Value |  
|:-----:|:--------:|  
| app | backend |  
| color | yellow |  
| shape | triangle |  
{{</admonition>}}

{{<admonition note "POD F-bgt" true>}}  
| Key | Value |  
|:-----:|:--------:|  
| app | backend |  
| color | green |  
| shape | triangle |  
{{</admonition>}}

{{<admonition note "POD G-frr" true>}}  
| Key | Value |  
|:-----:|:--------:|  
| app | frontend |  
| color | red |  
| shape | rectangle |  
{{</admonition>}}

{{<admonition note "POD H-byr" true>}}  
| Key | Value |  
|:-----:|:--------:|  
| app | backend |  
| color | yellow |  
| shape | rectangle |  
{{</admonition>}}

{{<admonition note "POD I-bgr" true>}}  
| Key | Value |  
|:-:|:-:|  
| app | backend |  
| color | green |  
| shape | rectangle |  
{{</admonition>}}

## Selector

### Selector 1

```shell
kubectl get pods --selector app=backend,shape=rectangle
```

| Key | Value |  
|:-:|:-:|  
| app | backend |  
| shape | rectangle |

{{<admonition result "Selected PODs by Selector 1" true>}}  
| POD |  
|:-:|  
| POD H-byr |  
| POD I-bgr |  
{{</admonition>}}

### Selector 2

```shell
kubectl get pods --selector app=frontend,color=red
```

| Key | Value |  
|:-:|:-:|  
| app | frontend |  
| color | red |

{{<admonition result "Selected PODs by Selector 2" true>}}  
| POD |  
|:-:|  
| POD A-frt |  
| POD G-frr |  
{{</admonition>}}

### Selector 3

```shell
kubectl get pods --selector color=green,shape=rectangle
```

| Key | Value |  
|:-:|:-:|  
| color | green |  
| shape | rectangle |

{{<admonition result "Selected PODs by Selector 2" true>}}  
| POD |  
|:-:|  
| POD I-bgr |  
{{</admonition>}}

## YAML

<sub>replicaset-definition.yml</sub>

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: simple-webapp
  labels:
    app: App1
    function: Front-end
spec:
  replicas: 3
  selector: # -> This selector is matching below labels in template.
    matchLabels:
      app: App1
  template:
    metadata:
      labels:
        app: App1
        function: Front-end
    spec:
      containers:
        - name: simple-webapp
          image: simple-webapp
```

<sub>service-definition.yml</sub>

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector: # -> This selector is find ReplicaSet and PODs.
    app: App1
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

## Annotations Example

<sub>replicaset-annotation-definition.yml</sub>

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: simple-webapp
  labels:
    app: App1
    function: Front-end
  annotations:
    buildVersion: 1.34
spec:
  replicas: 3
  selector: # -> This selector is matching below labels in template.
    matchLabels:
      app: App1
  template:
    metadata:
      labels:
        app: App1
        function: Front-end
    spec:
      containers:
        - name: simple-webapp
          image: simple-webapp
```