---
title: "Kubernetes 백업과 복원"

date: 2021-05-19T10:09:13+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- cluster maintenance
- backup
- restore

keywords:

- kubernetes
- cluster maintenance
- backup
- restore

---

## 백업 방법들

### 자원 구성 백업

{{<admonition note "자원 구성 백업" true>}}  
YAML 파일들 {{</admonition>}}

#### 명령

<sub>Create Resource</sub>

```shell
kubectl create namespace new-namespace
kubectl create secret
kubectl create configmap
```

<sub>Backup</sub>

```shell
# Backup All Resources
kubectl get all --all-namespaces -o yaml > all-deploy-services.yaml
```

#### 선언

{{<admonition note "추천" true>}}  
GitHub 에 저장하고 팀과 함께 관리!  
{{</admonition>}}

#### VELERO

{{<admonition note VELERO true>}}  
[Velero](https://velero.io/) 는 쿠버네티스 클러스터의 자원들과 영구 볼륨들의 이주나 재난 발생 회복을 위한 백업 및 복구 오픈소스 툴이다.  
{{</admonition>}}

### ETCD Cluster

{{<admonition note "ETCD Cluster" true>}}  
ETCD Snapshots  
{{</admonition>}}

#### ETCD 정보

##### ETCD 상세정보

```shell
kubectl -n kube-system describe pod etcd-master
```

##### ETCD Version

<sub>Image Version</sub>

```shell
kubectl -n kube-system describe pod etcd-master | grep -i image
```

##### ETCD Address

```shell
kubectl -n kube-system describe pod etcd-controlplane | grep -i listen-client
```

#### ETCD 백업과 복구

##### Snapshot 생성

```shell
ETCDCTL_API=3 etcdctl snapshot save snapshot.db
```

##### Snapshot 상태

```shell
ETCDCTL_API=3 etcdctl snapshot status snapshot.db
```

```shell
ETCDCTL_API=3 etcdctl snapshot save snapshot.db \
    --endpoints=https://127.0.0.1:2379 \
    --cacert=/etc/etcd/ca.crt \
    --cert=/etc/etcd/etcd-server.crt \
    --key=/etc/etcd/etcd-server.key
```

##### ETCD 복구

##### `kube-apiserver` 정지

```shell
service kube-apiserver stop
```

##### Snapshot 복구

###### 예제

```shell
ETCDCTL_API=3 etcdctl snapshot restore snapshot.db \
    --data-dir /var/lib/etcd-from-backup \
    --initial-cluster master-1=https://192.168.5.11:2380,master-2=https://192.168.5.12:2380 \
    --initial-cluster-token etcd-cluster-1 \
    --initial-advertise-peer-urls https://${INTERNAL_IP}:2380
```

###### `--data-dir` 옵션과 함께 Snapshot 복구

```shell
ETCDCTL_API=3 etcdctl snapshot restore /opt/snapshot-pre-boot.db \
    --data-dir /var/lib/etcd-from-backup
```

###### `etcd.yaml` 변경

```shell
vi /etc/kubernetes/manifests/etcd.yaml
```

###### `path` 변경

```yaml
  volumes:
    - hostPath:
        path: /var/lib/etcd-from-backup
        type: DirectoryOrCreate
      name: etcd-data
```

##### Daemon 리로드

```shell
systemctl daemon-reload
```

##### `etcd` 시작

```shell
service etcd restart
```

##### `kube-apiserver` 시작

```shell
service kube-apiserver start
```

### 영구 볼륨

{{<admonition note "Persistence Volumes" true>}}  
디스크  
{{</admonition>}}

