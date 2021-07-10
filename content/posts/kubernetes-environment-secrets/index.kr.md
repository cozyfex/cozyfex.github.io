---
title: "Kubernetes Environment Secrets"

date: 2021-05-17T01:18:07+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- application lifecycle management
- environment variable
- secret

keywords:

- kubernetes
- application lifecycle management
- environment variable
- secret

---

## Create Secret

{{<admonition note Secret true>}}  
`Secret` 의 자세한 내용은, 이 [문서](https://kubernetes.io/docs/concepts/configuration/secret/)를 확인한다.  
{{</admonition>}}

### 명령

#### 명령으로 값 설정

```shell
# Structure
kubectl create secret generic <secret-name> --from-literal=<key>=<value> \
                                       --from-literal=<key>=<value> \
                                       --from-literal=<key>=<value>
# Example
kubectl create secret generic app-secret --from-literal=DB_Host=mysql \
                                         --from-literal=DB_User=root \
                                         --from-literal=DB_Password=paswrd
```

#### 파일로 값 설정

<sub>app_secret.properties</sub>

```yaml
DB_Host: mysql
DB_User: root
DB_Password: paswrd
```

```shell
# Structure
kubectl create secret generic <secret-name> --from-file=<path-to-file>

# Example
kubectl create secret generic app-secret --from-file=app_secret.properties
```

### 선언

<sub>Encode</sub>

```shell
# bXlzcWw=
echo -n 'mysql' | base64

# cm9vdA==
echo -n 'root' | base64

# cGFzd3Jk
echo -n 'paswrd' | base64
```

<sub>Decode</sub>

```shell
# mysql
echo -n 'bXlzcWw=' | base64 --decode

# root
echo -n 'cm9vdA==' | base64 --decode

# paswrd
echo -n 'cGFzd3Jk' | base64 --decode
```

<sub>secret-data.yaml</sub>

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
data:
  DB_Host: bXlzcWw=
  DB_User: cm9vdA==
  DB_Password: cGFzd3Jk
```

```shell
kubectl create –f secret-data.yaml
```

## Secret 을 POD YAML 에 적용

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
      envFrom:
        - secretRef:
            name: app-secret
```

```shell
kubectl create -f pod-definition.yaml
```

## Secrets 확인

```shell
kubectl get secrets
```

## Secret 상세정보

```shell
# All Secrets
kubectl describe secrets

# Specific Secret
kubectl describe secret app-secret
```

## Secret 을 YAML 로 내보내기

```shell
kubectl get secret app-secret –o yaml > app-secret.yaml
```

## POD 들에서 Secret

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
        - secretRef:
            name: app-secret
        - secretRef:
            name: db-secret
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
        - name: DB_Password
          valueFrom:
            secretKeyRef:
              name: app-secret
              key: DB_Password
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
    - name: app-secret-volume
      secret:
        name: app-secret
  containers:
    - name: simple-webapp-color
      image: simple-webapp-color
      ports:
        - containerPort: 8080
      volumeMounts:
        - name: app-secret-volume
          mountPath: /etc/secret
```

