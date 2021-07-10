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
If you want to know all of `Secret`, see this [document](https://kubernetes.io/docs/concepts/configuration/secret/)  
{{</admonition>}}

### Imperative

#### Values with command

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

#### Values from file

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

### Declarative

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

## Apply Secret to POD YAML

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

## View Secrets

```shell
kubectl get secrets
```

## Secret Detail

```shell
# All Secrets
kubectl describe secrets

# Specific Secret
kubectl describe secret app-secret
```

## Export Secret to YAML

```shell
kubectl get secret app-secret –o yaml > app-secret.yaml
```

## Secret in PODs

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

