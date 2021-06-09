---
title: "Kubernetes Persistent Volumes"

date: 2021-05-28T22:38:18+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- storage
- volumes
- persistent

keywords:

- kubernetes
- storage
- volumes
- persistent

---

## Persistent Volume

### `pv-definition.yaml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-vol1
spec:
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  capacity:
    storage: 1Gi
  awsElasticBlockStrage:
    volumeID: <volume-id>
    fsType: ext4
```

### Access Modes

| Access Mode | CLI | Description |  
|:-:|:-:|:-|  
| ReadWriteOnce | RWO | 싱클 노드에서만 읽기/쓰기 가능한 마운트된 볼륨 |  
| ReadOnlyMany | ROX | 다중 노드에서 읽기만 가능한 마운트된 볼륨 |  
| ReadWriteMany | RWX | 다중 노드에서 읽기/쓰기 가능한 마운트된 볼륨 |

### Reclaim Policy

| Reclaim Policy | Description |  
|:-:|:-|  
| Retain | 수동 교정 |  
| Recycle | 기본 취소(`rm -rf /thevolume/*`) |  
| Delete | AWS EBS, GCE PD, Azure Disk, OpenStack Cinder 연관된 볼륨이 지워진다. |

### Commands

```shell
kubectl create -f pv-definition.yaml
```

```shell
kubectl get persistentvolume
kubectl get pv
```

## Persistent Volume Claim

### Binding Rule

| Rule | Description |  
|:-:|:-|  
| Sufficient Capacity | PV Capacity |  
| Access Modes | ReadOnlyMany / ReadWriteOnce / ReadWriteMany |  
| Volume Modes | Filesystem / Block |  
| Storage Class | Class |  
| Selector | matchLabels |

### `pvc-definition.yaml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

### Commands

#### PVC 생성

```shell
kubectl create -f pvc-definition.yaml
```

#### PVC 목록

```shell
kubectl get persistentvolumeclaim
kubectl get pvc
```

#### PVC 삭제

```shell
kubectl delete persistentvolumeclaim myclaim
```

## POD 에 PVC 마운트

{{<admonition note Mount true>}}  
이 방법은 ReplicaSet, Deployment 의 `template` 섹션에서 모두 같다.  
{{</admonition>}}

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
        - mountPath: "/var/www/html"
          name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
```

## Storage Class

### Static Provisioning

```shell
gcloud beta compute disks create \
  --size 1GB
  --region us-east1
  pd-disk
```

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-vol1
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 500Mi
  gcePersistentDisk:
    pdName: pd-disk
    fsType: ext4
```

### Dynamic Provisioning

#### Storage Class

{{<admonition note "Storage Class" true>}}  
공식 문서 확인 [Storage Class](https://kubernetes.io/docs/concepts/storage/storage-classes/)  
{{</admonition>}}

<sub>sc-definition.yaml</sub>

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: google-storage
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-standard
  replication-type: none
```

| Provisioner | Parameter | Values |  
|:-:|:-:|:-:|  
| kubernetes.io/gce-pd | type | pd-standard / pd-ssd |  
| kubernetes.io/gce-pd | replication-type | none / regional_pd |

#### PVC 에서 Storage Class

<sub>pvc-definition.yaml</sub>

```yaml
apiVersion: v1
kind: PersistentVolumClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: google-storage
  resources:
    requests:
      storage: 500Mi
```

#### POD 에서 PVC

<sub>pod-definition.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: random-number-generator
spec:
  containers:
    - name: alpine
      image: alpine
      command: [ "/bin/sh", "-c" ]
      args: [ "shuf -i 0-100 -n 1 >> /opt/number.out;" ]
      volumeMounts:
        - mountPath: /opt
          name: data-volume
  volumes:
    - name: data-volume
      persistentVolumeClaim:
        claimName: myclaim
```

#### Storage Class 레벨링

<sub>Silver Storage Class</sub>

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: silver
provisioner: kubernetes.io/gce-pd
  type: pd-standard
  replication-type: none
```

<sub>Gold Storage Class</sub>

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gold
provisioner: kubernetes.io/gce-pd
  type: pd-ssd
  replication-type: none
```

<sub>Platinum Storage Class</sub>

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: platinum
provisioner: kubernetes.io/gce-pd
  type: pd-ssd
  replication-type: regional-pd
```

#### Storage Class Commands

```shell
kubectl get storageclass
kubectl get sc
```
