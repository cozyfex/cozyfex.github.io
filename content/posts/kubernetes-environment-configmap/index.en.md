---
title: "Kubernetes Environment Configmap"

date: 2021-05-17T00:35:10+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- application lifecycle management
- environment variable
- configmap

keywords:

- kubernetes
- application lifecycle management
- environment variable
- configmap

---

## Create ConfigMap

{{<admonition note ConfigMap true>}}  
If you want to know all of `ConfigMap`, see this [document](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)  
{{</admonition>}}

### Imperative

#### Values with command

```shell
# Structure
kubectl create configmap <config-name> --from-literal=<key>=<value> \
                                       --from-literal=<key>=<value> \
                                       --from-literal=<key>=<value>
# Example
kubectl create configmap app-config --from-literal=APP_COLOR=blue \
                                    --from-literal=APP_MODE=prod
```

#### Values from file

<sub>app_config.properties</sub>

```yaml
APP_COLOR: blue
APP_MODE: prod
```

```shell
# Structure
kubectl create configmap <config-name> --from-file=<path-to-file>

# Example
kubectl create configmap app-config --from-file=app_config.properties
```

### Declarative

<sub>config-map.yaml</sub>

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: blue
  APP_MODE: prod
```

```shell
kubectl create –f config-map.yaml
```

## Apply ConfigMap to POD YAML

<sub>pod-definition.yaml</sub>

```yaml
apiVersion: v1
kind:
  Pod
metadata:
  name: simple-webapp-color
spec:
  containers:
    - name: simple-webapp-color
      image: simple-webapp-color
      ports:
        - containerPort: 8080
      env:
        - name: APP_COLOR
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_COLOR
```

```shell
kubectl create -f pod-definition.yaml
```

## View ConfigMaps

```shell
kubectl get cm
kubectl get configmaps
```

## ConfigMap Detail

```shell
# All ConfigMaps
kubectl describe configmaps

# Specific ConfigMap
kubectl describe configmap app-config
```

## ConfigMap in PODs

### ENV

```yaml
apiVersion: v1
kind:
  Pod
metadata:
  name: simple-webapp-color
spec:
  containers:
    - name: simple-webapp-color
      image: simple-webapp-color
      ports:
        - containerPort: 8080
      envFrom:
        - configMapRef:
            name: app-config
        - configMapRef:
            name: db-config
```

### SINGLE ENV

```yaml
apiVersion: v1
kind:
  Pod
metadata:
  name: simple-webapp-color
spec:
  containers:
    - name: simple-webapp-color
      image: simple-webapp-color
      ports:
        - containerPort: 8080
      env:
        - name: APP_COLOR
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: APP_COLOR
```

### VOLUME

```yaml
apiVersion: v1
kind:
  Pod
metadata:
  name: simple-webapp-color
spec:
  volumes:
    - name: config-volume
      configMap:
        name: app-config
  containers:
    - name: simple-webapp-color
      image: simple-webapp-color
      ports:
        - containerPort: 8080
      volumeMounts:
        - name: config-volume
          mountPath: /etc/config
```

