---
title: "Kubernetes Manual Test"

date: 2021-06-05T17:54:54+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- test

tags:

- kubernetes
- test
- manual

keywords:

- kubernetes
- test
- manual

---

## Kubernetes Status

### Nodes

```shell
kubectl get nodes
```

```
NAME       STATUS   ROLES                  AGE     VERSION
master01   Ready    control-plane,master   40h     v1.21.1
master02   Ready    control-plane,master   40h     v1.21.1
node01     Ready    <none>                 7h57m   v1.21.1
node02     Ready    <none>                 139m    v1.21.1
```

### PODs

```shell
kubectl get pods --all-namespaces
```

```
NAMESPACE     NAME                               READY   STATUS    RESTARTS   AGE
kube-system   coredns-558bd4d5db-8hzd2           1/1     Running   2          40h
kube-system   coredns-558bd4d5db-hdhwx           1/1     Running   2          40h
kube-system   etcd-master01                      1/1     Running   8          40h
kube-system   etcd-master02                      1/1     Running   8          40h
kube-system   kube-apiserver-master01            1/1     Running   11         40h
kube-system   kube-apiserver-master02            1/1     Running   24         40h
kube-system   kube-controller-manager-master01   1/1     Running   4          40h
kube-system   kube-controller-manager-master02   1/1     Running   10         40h
kube-system   kube-proxy-cmph2                   1/1     Running   2          7h59m
kube-system   kube-proxy-drxbm                   1/1     Running   1          141m
kube-system   kube-proxy-qcwh7                   1/1     Running   5          40h
kube-system   kube-proxy-tgdzj                   1/1     Running   3          40h
kube-system   kube-scheduler-master01            1/1     Running   4          40h
kube-system   kube-scheduler-master02            1/1     Running   10         40h
kube-system   weave-net-f8t5j                    2/2     Running   5          154m
kube-system   weave-net-rzskn                    2/2     Running   3          141m
kube-system   weave-net-sl9gq                    2/2     Running   4          154m
kube-system   weave-net-tcdxq                    2/2     Running   5          154m
```

## Run Test

### Run POD

```shell
kubectl run nginx --image=nginx
```

```
pod/nginx created
```

```shell
kubectl get pods
```

```
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          20s
```

### Deployment

#### Create a Deployment

<sub>nginx-deployment.yaml</sub>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx-continaer
          image: nginx
          ports:
            - containerPort: 80
```

```shell
kubectl create -f nginx-deployment.yaml
```

#### Check Deployment

```shell
kubectl get deploy
```

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2/2     2            2           42s
```

```shell
kubectl get pods
```

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx                               1/1     Running   0          16m
nginx-deployment-588c8fc79c-9n8ns   1/1     Running   0          110s
nginx-deployment-588c8fc79c-g56l8   1/1     Running   0          110s
```

#### Scale

```shell
kubectl scale --replicas=3 deploy/nginx-deployment
```

```
deployment.apps/nginx-deployment scaled
```

```shell
kubectl get deploy
```

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           3m58s
```

```shell
kubectl get pods
```

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx                               1/1     Running   0          19m
nginx-deployment-588c8fc79c-9n8ns   1/1     Running   0          4m52s
nginx-deployment-588c8fc79c-g56l8   1/1     Running   0          4m52s
nginx-deployment-588c8fc79c-zx88c   1/1     Running   0          90s
```

#### Expose

```shell
kubectl get svc
```

```
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   41h
```

```shell
kubectl expose deploy nginx-deployment --port=80 --type=NodePort
```

```
service/nginx-deployment exposed
```

```shell
kubectl get svc
```

```
NAME               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP   10.96.0.1      <none>        443/TCP        41h
nginx-deployment   NodePort    10.103.90.37   <none>        80:32377/TCP   29s
```

```shell
curl http://10.0.2.100:32377
```

```html
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to nginx!</title>
    <style>
        body {
            width: 35em;
            margin: 0 auto;
            font-family: Tahoma, Verdana, Arial, sans-serif;
        }
    </style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
    working. Further configuration is required.</p>

<p>For online documentation and support please refer to
    <a href="http://nginx.org/">nginx.org</a>.<br/>
    Commercial support is available at
    <a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```