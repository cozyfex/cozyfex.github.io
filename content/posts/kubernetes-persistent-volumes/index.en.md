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
| ReadWriteOnce | RWO | The volume can be mounted as read-write by a single node |  
| ReadOnlyMany | ROX | The volume can be mounted read-only by many nodes |  
| ReadWriteMany | RWX | The volume can be mounted as read-write by many nodes |

### Reclaim Policy

| Reclaim Policy | Description |  
|:-:|:-|  
| Retain | Manual reclamation |  
| Recycle | Basic scrub(`rm -rf /thevolume/*`) |  
| Delete | Associated storage asset such as AWS EBS, GCE PD, Azure Disk, or OpenStack Cinder volume is deleted |

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

#### Create a PVC

```shell
kubectl create -f pvc-definition.yaml
```

#### PVC List

```shell
kubectl get persistentvolumeclaim
kubectl get pvc
```

#### Delete PVC

```shell
kubectl delete persistentvolumeclaim myclaim
```

## PVC Mount to POD

{{<admonition note Mount true>}}  
This is a same way for ReplicaSet, Deployment in `template` section.  
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
Check official [Storage Class](https://kubernetes.io/docs/concepts/storage/storage-classes/)  
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

#### Storage Class to PVC

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

#### PVC to POD

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

#### Storage Class Leveling

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
