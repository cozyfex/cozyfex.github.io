---
title: "Kubernetes Review"

date: 2021-06-21T20:25:25+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- review

keywords:

- kubernetes
- review

---

## Set Default Namespace

```shell
kubectl config set-context --current --namespace=alpha
```

## Create a POD

```shell
kubectl run nginx-pod --image=nginx:alpine --restart=Never --namespace=alpha
```

## Create a Deployment

```shell
kubectl create deploy webapp --image=nginx:alpine --replicas=3
```

## Scale

```shell
kubectl scale deploy webapp --replicas=1
```

## History

```shell
kubectl rollout history development webapp
```

## Create a Service

```shell
kubectl expose deploy/webapp --port=8080 --target-port=8080 --type=ClusterIP
```

## Create a Role & RoleBinding

```shell
kubectl create role developer-role --resource=pods --verb=create,list,get,update,delete --namespace=delta
```

```shell
kubectl create rolebinding developer-role-binding --role=developer-role --user=john --namespace=delta
```

## Create a ClusterRole & ClusterRoleBinding

```shell
kubectl create clsuterrole dev-cluster-role --resource=developments --verb=create,list,get,update,delete
```

```shell
kubectl create clusterrolebinding dev-cluster-role-binding --clusterrole=dev-cluster-role --user=john
```

## ETCD

### Backup

```shell
ETCDCTL_API=3 etcdctl --cacert=/etc/kubernetes/pki/etcd/ca.crt \
                      --cert=/etc/kubernetes/pki/etcd/server.crt \
                      --key=/etc/kubernetes/pki/etcd/server.key \
                      snapshot save /opt/snapshot-pre-boot.db
```

### Restore

#### Restore ETCD

```shell
ETCDCTL_API=3 etcdctl --cacert=/etc/kubernetes/pki/etcd/ca.crt \
                      --cert=/etc/kubernetes/pki/etcd/server.crt \
                      --key=/etc/kubernetes/pki/etcd/server.key \
                      --data-dir=/var/lib/etcd-new \
                      snapshot restore /opt/snapshot-pre-boot.db
```

#### Change ETCD Config

```shell
vi /etc/kubernetes/manifests/etcd.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.168.49.2:2379
  creationTimestamp: null
  labels:
    component: etcd
    tier: control-plane
  name: etcd
  namespace: kube-system
spec:
  containers:
    - command:
        - etcd
        - --advertise-client-urls=https://192.168.49.2:2379
        - --cert-file=/var/lib/minikube/certs/etcd/server.crt
        - --client-cert-auth=true
        - --data-dir=/var/lib/etcd-new # Change here and...
        - --initial-advertise-peer-urls=https://192.168.49.2:2380
        - --initial-cluster=minikube=https://192.168.49.2:2380
        - --key-file=/var/lib/minikube/certs/etcd/server.key
        - --listen-client-urls=https://127.0.0.1:2379,https://192.168.49.2:2379
        - --listen-metrics-urls=http://127.0.0.1:2381
        - --listen-peer-urls=https://192.168.49.2:2380
        - --name=minikube
        - --peer-cert-file=/var/lib/minikube/certs/etcd/peer.crt
        - --peer-client-cert-auth=true
        - --peer-key-file=/var/lib/minikube/certs/etcd/peer.key
        - --peer-trusted-ca-file=/var/lib/minikube/certs/etcd/ca.crt
        - --proxy-refresh-interval=70000
        - --snapshot-count=10000
        - --trusted-ca-file=/var/lib/minikube/certs/etcd/ca.crt
      image: k8s.gcr.io/etcd:3.4.13-0
      imagePullPolicy: IfNotPresent
      livenessProbe:
        failureThreshold: 8
        httpGet:
          host: 127.0.0.1
          path: /health
          port: 2381
          scheme: HTTP
        initialDelaySeconds: 10
        periodSeconds: 10
        timeoutSeconds: 15
      name: etcd
      resources:
        requests:
          cpu: 100m
          ephemeral-storage: 100Mi
          memory: 100Mi
      startupProbe:
        failureThreshold: 24
        httpGet:
          host: 127.0.0.1
          path: /health
          port: 2381
          scheme: HTTP
        initialDelaySeconds: 10
        periodSeconds: 10
        timeoutSeconds: 15
      volumeMounts:
        - mountPath: /var/lib/etcd-new # Change here and...
          name: etcd-data
        - mountPath: /var/lib/minikube/certs/etcd
          name: etcd-certs
  hostNetwork: true
  priorityClassName: system-node-critical
  volumes:
    - hostPath:
        path: /var/lib/minikube/certs/etcd
        type: DirectoryOrCreate
      name: etcd-certs
    - hostPath:
        path: /var/lib/etcd-new # Change here!
        type: DirectoryOrCreate
      name: etcd-data
status: { }
```

## Extra Options for New Scheduler

```shell
cp -ai /etc/kubernetes/manifests/kube-scheduler.yaml /etc/kubernetes/manifests/my-scheduler.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    component: kube-scheduler
    tier: control-plane
  name: my-scheduler # Change here and...
  namespace: kube-system
spec:
  containers:
    - command:
        - kube-scheduler
        - --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
        - --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
        - --bind-address=127.0.0.1
        - --kubeconfig=/etc/kubernetes/scheduler.conf
        - --leader-elect=false # Change here and...
        - --port=30291 # Change here and...
        - --scheduler-name=my-scheduler # Add here and...
        - --secure-port=0 # Add here and...
      image: k8s.gcr.io/kube-scheduler:v1.20.2
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
      name: kube-scheduler
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

## CNI

### Default CNI Binaries Location

```shell
cd /etc/cni/net.d
```

### Check PODs IP Range

```shell
kubectl -n kube-system logs weave-net-8lwzf -c weave | grep range
```

### Check Services IP Range

```shell
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep range
```

### Check Proxy Type

```shell
kubectl -n kube-system logs kube-proxy-jjwkz | grep proxy
```

## Docker Logs

### Check Docker Container Process

```shell
docker ps -a
```

### Logs

```shell
docker logs <container-id>
```

## POD Logs

```shell
kubectl logs nginx-pod
```

## Check Linux Process

### `journalctl`

```shell
journalctl -u kubelet
```

### `systemctl status`

```shell
systemctl status kubelet
```

## NetworkPolicy

```shell
kubectl get netpol
```

## Generate a YAML

```shell
kubectl run nginx-pod --image=nginx:alpine --restart=Never --namespace=alpha --dry-run=client -o yaml > nginx-pod.yaml
```

## Execute Command

### With Exist POD

```shell
kubectl exec nginx-pod -- nslookup webapp-service
```

### Temporally POD

```shell
kubectl run test-pod --image=busybox --restart=Never --rm -it -- nc -z -v -w 2 webapp-service
```



