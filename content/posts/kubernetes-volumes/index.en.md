---
title: "Kubernetes Volumes"

date: 2021-05-28T22:28:26+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- storage
- volumes

keywords:

- kubernetes
- storage
- volumes

---

## Volumes & Mounts

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: random-number-generator
spec:
  containers:
    - image: alpine
      name: alpine
      command: [ "/bin/sh", "-c" ]
      args: [ "shuf -i 0-100 -n 1 >> /opt/number.out;" ]
      volumeMounts:
        - mountPath: /opt
          name: data-volume
  volumes:
    - name: data-volume
      hostPath:
        path: /data
        type: Directory
```

## Volume Types

- NFS
- GlusterFS
- Flocker
- ceph
- SCALEIO
- AWS
- Google Cloud
- Azure

```yaml
spec:
  volumes:
    - name: data-volume
      awsElasticBlockStore:
        volumeID: <volume-id>
        fsType: ext4
```


