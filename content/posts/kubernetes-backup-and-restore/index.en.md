---
title: "Kubernetes Backup and Restore"

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

## Backup Candidate

### Resource Configuration

{{<admonition note "Resource Configuration" true>}}  
YAML Files  
{{</admonition>}}

#### Imperative

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

#### Declarative

{{<admonition note Recommendation true>}}  
Save to GitHub and manage with a team!  
{{</admonition>}}

#### VELERO

{{<admonition note VELERO true>}}  
[Velero](https://velero.io/) is an open source tool to safely backup and restore, perform disaster recovery, and migrate Kubernetes cluster resources and persistent volumes.  
{{</admonition>}}

### ETCD Cluster

{{<admonition note "ETCD Cluster" true>}}  
ETCD Snapshots  
{{</admonition>}}

#### ETCD Information

##### ETCD Service

```shell
cat /etc/systemd/system/etcd.service
```

```shell

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

#### Backup And Restore ETCD

##### Create A Snapshot

```shell
ETCDCTL_API=3 etcdctl snapshot save snapshot.db
```

##### Status Of Snapshot

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

##### Restore ETCD

##### Stop `kube-apiserver`

```shell
service kube-apiserver stop
```

##### Restore Snapshot

###### Sample

```shell
ETCDCTL_API=3 etcdctl snapshot restore snapshot.db \
    --data-dir /var/lib/etcd-from-backup \
    --initial-cluster master-1=https://192.168.5.11:2380,master-2=https://192.168.5.12:2380 \
    --initial-cluster-token etcd-cluster-1 \
    --initial-advertise-peer-urls https://${INTERNAL_IP}:2380
```

###### Restore snapshot with `--data-dir`

```shell
ETCDCTL_API=3 etcdctl snapshot restore /opt/snapshot-pre-boot.db \
    --data-dir /var/lib/etcd-from-backup
```

###### Change the `etcd.yaml`

```shell
vi /etc/kubernetes/manifests/etcd.yaml
```

###### Change the `path`

```yaml
  volumes:
    - hostPath:
        path: /var/lib/etcd-from-backup
        type: DirectoryOrCreate
      name: etcd-data
```

##### Reload Daemon

```shell
systemctl daemon-reload
```

##### Start `etcd`

```shell
service etcd restart
```

##### Start `kube-apiserver`

```shell
service kube-apiserver start
```

### Persistence Volumes

{{<admonition note "Persistence Volumes" true>}}  
Disk  
{{</admonition>}}

