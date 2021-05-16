---
title: "Kubernetes Multiple Schedulers"

date: 2021-05-16T17:17:49+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- scheduler
- multiple scheduler

keywords:

- kubernetes
- scheduler
- multiple scheduler

---

## Deploy Additional Scheduler

```shell
cd /etc/systemd/system
cp -ai kube-scheduler.service my-custom-scheduler.service
vi my-custom-scheduler.service
```

<sub>kube-scheduler.service</sub>

```
ExecStart=/usr/local/bin/kube-scheduler \\
    --config=/etc/kubernetes/config/kube-scheduler.yml \\
    --scheduler-name=kube-scheduler
```

<sub>my-custom-scheduler.service</sub>

```
ExecStart=/usr/local/bin/kube-scheduler \\
    --config=/etc/kubernetes/config/kube-scheduler.yml \\
    --scheduler-name=my-custom-scheduler
```

## Scheduler YAML

<sub>/etc/kubernetes/manifests/kube-scheduler.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kube-scheduler
  namespace: kube-system
spec:
  containers:
    - command:
        - kube-scheduler
        - --address=127.0.0.1
        - --kubeconfig=/etc/kubernetes/scheduler.conf
        - --leader-elect=true
      image: k8s.gcr.io/kube-scheduler-amd64:v1.11.3
      name: kube-scheduler
```

<sub>~/my-custom-scheduler.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-custom-scheduler
  namespace: kube-system
spec:
  containers:
    - command:
        - kube-scheduler
        - --address=127.0.0.1
        - --kubeconfig=/etc/kubernetes/scheduler.conf
        - --leader-elect=false
        - --scheduler-name=my-custom-scheduler
        - --lock-object-name=my-custom-scheduler
      image: k8s.gcr.io/kube-scheduler-amd64:v1.11.3
      name: my-custom-scheduler
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    scheduler.alpha.kubernetes.io/critical-pod: ""
  creationTimestamp: null
  labels:
    component: my-scheduler
    tier: control-plane
  name: my-scheduler
  namespace: kube-system
spec:
  containers:
    - command:
        - kube-scheduler
        - --address=127.0.0.1
        - --kubeconfig=/etc/kubernetes/scheduler.conf
        - --leader-elect=false
        - --port=10282
        - --scheduler-name=my-scheduler
        - --secure-port=0
      image: k8s.gcr.io/kube-scheduler-amd64:v1.16.0
      imagePullPolicy: IfNotPresent
      livenessProbe:
        failureThreshold: 8
        httpGet:
          host: 127.0.0.1
          path: /healthz
          port: 10282
          scheme: HTTP
        initialDelaySeconds: 15
        timeoutSeconds: 15
      name: kube-scheduler
      resources:
        requests:
          cpu: 100m
      volumeMounts:
        - mountPath: /etc/kubernetes/scheduler.conf
          name: kubeconfig
          readOnly: true
  hostNetwork: true
  priorityClassName: system-cluster-critical
  volumes:
    - hostPath:
        path: /etc/kubernetes/scheduler.conf
        type: FileOrCreate
      name: kubeconfig
status: { }
```

## Create a Scheduler in exist Cluster

### Copy from /etc/kubernetes/manifests/kube-scheduler.yaml

```shell
cp -ai /etc/kubernetes/manifests/kube-scheduler.yaml ./my-custom.yaml
```

### Configure YAML

<sub>my-scheduler.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    component: kube-scheduler
    tier: control-plane
  name: my-scheduler # Change name
  namespace: kube-system
spec:
  containers:
    - command:
        - kube-scheduler
        - --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
        - --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
        - --bind-address=127.0.0.1
        - --kubeconfig=/etc/kubernetes/scheduler.conf
        - --leader-elect=false
        - --port=0
        - --scheduler-name=my-scheduler # Add this option
      image: k8s.gcr.io/kube-scheduler:v1.19.0
      imagePullPolicy: IfNotPresent
      livenessProbe:
        failureThreshold: 8
        httpGet:
          host: 127.0.0.1
          path: /healthz
          port: 10259
          scheme: HTTPS
        initialDelaySeconds: 10
        periodSeconds: 10
        timeoutSeconds: 15
      name: my-scheduler # Change name
      resources:
        requests:
          cpu: 100m
      startupProbe:
        failureThreshold: 24
        httpGet:
          host: 127.0.0.1
          path: /healthz
          port: 10259
          scheme: HTTPS
        initialDelaySeconds: 10
        periodSeconds: 10
        timeoutSeconds: 15
      volumeMounts:
        - mountPath: /etc/kubernetes/scheduler.conf
          name: kubeconfig
          readOnly: true
  hostNetwork: true
  priorityClassName: system-node-critical
  volumes:
    - hostPath:
        path: /etc/kubernetes/scheduler.conf
        type: FileOrCreate
      name: kubeconfig
status: { }
```

### Create a Scheduler

```shell
kubectl create -f my-scheduler.yaml
```

## Set Specific Scheduler to a `POD`

<sub>pod-definition.yml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx-container
      image: nginx
  schedulerName: my-custom-scheduler
```

```shell
kubectl create -f pod-definition.yml
```

{{<admonition note "What if the schedulerName is not correctly?" true>}}  
The `POD` is in Pending Status.  
{{</admonition>}}

## View Events

```shell
kubectl get events
```

## View Scheduler Logs

```shell
kubectl logs my-custom-scheduler --name-space=kube-system
```