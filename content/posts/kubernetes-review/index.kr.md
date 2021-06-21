---
title: "Kubernetes 리뷰"

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

## 기본 Namespace 설정

```shell
kubectl config set-context --current --namespace=alpha
```

## POD 생성

```shell
kubectl run nginx-pod --image=nginx:alpine --restart=Never --namespace=alpha
```

## Deployment 생성

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

## Service 생성

```shell
kubectl expose deploy/webapp --port=8080 --target-port=8080 --type=ClusterIP
```

## Role & RoleBinding 생성

```shell
kubectl create role developer-role --resource=pods --verb=create,list,get,update,delete --namespace=delta
```

```shell
kubectl create rolebinding developer-role-binding --role=developer-role --user=john --namespace=delta
```

## ClusterRole & ClusterRoleBinding 생성

```shell
kubectl create clsuterrole dev-cluster-role --resource=developments --verb=create,list,get,update,delete
```

```shell
kubectl create clusterrolebinding dev-cluster-role-binding --clusterrole=dev-cluster-role --user=john
```

## ETCD

### 백업

```shell
ETCDCTL_API=3 etcdctl --cacert=/etc/kubernetes/pki/etcd/ca.crt \
                      --cert=/etc/kubernetes/pki/etcd/server.crt \
                      --key=/etc/kubernetes/pki/etcd/server.key \
                      snapshot save /opt/snapshot-pre-boot.db
```

### 복구

#### ETCD 복구

```shell
ETCDCTL_API=3 etcdctl --cacert=/etc/kubernetes/pki/etcd/ca.crt \
                      --cert=/etc/kubernetes/pki/etcd/server.crt \
                      --key=/etc/kubernetes/pki/etcd/server.key \
                      --data-dir=/var/lib/etcd-new \
                      snapshot restore /opt/snapshot-pre-boot.db
```

#### ETCD 설정 변경

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

## 새 Scheduler 를 위한 추가 옵션 설정

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

### CNI 실행파일 기본 위치

```shell
cd /etc/cni/net.d
```

### PODs IP 범위 확인

```shell
kubectl -n kube-system logs weave-net-8lwzf -c weave | grep range
```

### Services IP 범위 확인

```shell
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep range
```

### Proxy Type 확인

```shell
kubectl -n kube-system logs kube-proxy-jjwkz | grep proxy
```

## Docker 로그

### Docker 컨테이너 프로세스

```shell
docker ps -a
```

### 로그

```shell
docker logs <container-id>
```

## POD 로그

```shell
kubectl logs nginx-pod
```

## Linux 프로세스 확인

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

## YAML 파일 생성

```shell
kubectl run nginx-pod --image=nginx:alpine --restart=Never --namespace=alpha --dry-run=client -o yaml > nginx-pod.yaml
```

## 명령 실행

### 기존 POD 에서

```shell
kubectl exec nginx-pod -- nslookup webapp-service
```

### 임시 POD 에서

```shell
kubectl run test-pod --image=busybox --restart=Never --rm -it -- nc -z -v -w 2 webapp-service
```



