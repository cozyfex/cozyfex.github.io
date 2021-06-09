---
title: "Kubernetes Docker Storage"

date: 2021-05-28T21:14:45+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- docker
- storage

tags:

- kubernetes
- storage
- docker
- storage driver
- volume driver

keywords:

- kubernetes
- storage
- docker
- storage driver
- volume driver

---

## Docker File System

```shell
/var/lib/docker
├── aufs
├── containers
├── image
├── volumes
```

## Layered Architecture

### First Dockerfile

```dockerfile
FROM ubuntu

RUN apt-get update && apt-get -y install python

RUN pip install flask flask-mysql

COPY . /opt/source-code

ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run
```

```shell
docker build -t Dockerfile cozyfex/my-custom-app
```

{{<mermaid>}}

graph TD;

L1(Layer 1. Base Ubuntu Layer - 120MB) --> L2(Layer 2. Changes in apt packages - 306MB)  
L2 --> L3(Layer 3. Change in pip packages - 6.3MB)  
L3 --> L4(Layer 4. Source code - 229B)
L4 --> L5(Layer 5. Update Entrypoint - 0B)

{{</mermaid>}}

### Second Dockerfile

```dockerfile
FROM ubuntu

RUN apt-get update && apt-get -y install python

RUN pip install flask flask-mysql

COPY app2.py /opt/source-code

ENTRYPOINT FLASK_APP=/opt/source-code/app2.py flask run
```

```shell
docker build -t Dockerfile cozyfex/my-custom-app2
```

{{<mermaid>}}

graph TD;

L1(Layer 1. Base Ubuntu Layer - Cached) --> L2(Layer 2. Changes in apt packages - Cached)  
L2 --> L3(Layer 3. Change in pip packages - Cached)  
L3 --> L4(Layer 4. Source code - 300B)
L4 --> L5(Layer 5. Update Entrypoint - 0B)

{{</mermaid>}}

### Run Container

#### Image Layers(Read Only Layer)

```shell
docker build -t Dockerfile cozyfex/my-custom-app
```

{{<mermaid>}}

graph TD;

L1(Layer 1. Base Ubuntu Layer - 120MB) --> L2(Layer 2. Changes in apt packages - 306MB)  
L2 --> L3(Layer 3. Change in pip packages - 6.3MB)  
L3 --> L4(Layer 4. Source code - 229B)
L4 --> L5(Layer 5. Update Entrypoint - 0B)

{{</mermaid>}}

#### Container Layer(Read Write Layer)

```shell
docker run cozyfex/my-custom-app
```

{{<mermaid>}}

graph TD;

L6(Layer 6. Container Layer)

{{</mermaid>}}

#### Exit Container

{{<admonition note IMPORTANT true>}}  
After exit container, there's no file remain.  
{{</admonition>}}

## Volumes

### Create a Volume

```shell
docker volume create data_volume
```

```shell
/var/lib/docker
├── aufs
├── containers
├── image
├── volumes
│   ├── data_volume
```

### Link Volume to Container

```shell
docker run -v data_volume:/var/lib/mysql mysql
```

### Link Host Volume to Container

```shell
docker run -v /data/mysql:/var/lib/mysql mysql
```

```shell
docker run --mount type=bind,source=/data/mysql,target=/var/lib/mysql mysql
```

## Docker Storage

### Storage Drivers

- [AUFS](https://docs.docker.com/storage/storagedriver/aufs-driver/)
- [ZFS](https://docs.docker.com/storage/storagedriver/zfs-driver/)
- [BTRFS](https://docs.docker.com/storage/storagedriver/btrfs-driver/)
- [Device Mapper](https://docs.docker.com/storage/storagedriver/device-mapper-driver/)
- [Overlay](https://docs.docker.com/storage/storagedriver/overlayfs-driver/)

### Volume Drivers

- Local
- Azure File Storage
- Convoy
- DigitalOcean Block Storage
- Flocker
- gce-docker
- GlusterFS
- NetApp
- RexRay
- Portworx
- VMWare vShpere Storage

#### Volume Mount

```shell
docker run -it \
  --name mysql
  --volume-driver rexray/ebs
  --mount src=ebs-vol,target=/var/lib/mysql
  mysql
```

## Container Structure

{{<mermaid>}}

graph TD;

C(Container Service) --> R(Container Runtime Interface)  
C --> S(Container Storage Interface)  
C --> N(Container Network Interface)

{{</mermaid>}}

### CRI(Container Runtime Interface)

- [docker](https://www.docker.com/)
- [rkt](https://github.com/rkt/rkt)
- [cri-o](https://cri-o.io/)

### CSI(Container Storage Interface)

- [portworx](https://portworx.com/)
- [AWS EBS](https://aws.amazon.com/)
- [DELL EMC](https://www.delltechnologies.com/)
- [GlusterFS](https://www.gluster.org/)

### CNI(Container Network Interface)

- [weaveworks](https://www.weave.works/)
- [flannel](https://github.com/flannel-io/flannel)
- [cilium](https://cilium.io/)

