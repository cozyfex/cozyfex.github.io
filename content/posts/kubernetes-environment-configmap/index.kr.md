---
title: "Kubernetes Environment Configmap"

date: 2021-05-17T00:35:10+09:00

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
`ConfigMap` 의 더 자세한 내용은, [문서](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) 링크를 확인하면 된다.  
{{</admonition>}}

### 명령

#### 명령으로 값 설정

```shell
# Structure
kubectl create configmap <config-name> --from-literal=<key>=<value> \
                                       --from-literal=<key>=<value> \
                                       --from-literal=<key>=<value>
# Example
kubectl create configmap app-config --from-literal=APP_COLOR=blue \
                                    --from-literal=APP_MODE=prod
```

#### 파일로 값 설정

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

### 선언

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

## ConfigMap 을 POD YAML 에 적용

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

## ConfigMaps 확인

```shell
kubectl get cm
kubectl get configmaps
```

## ConfigMap 상세정보

```shell
# All ConfigMaps
kubectl describe configmaps

# Specific ConfigMap
kubectl describe configmap app-config
```

## POD 들에서 ConfigMap

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

