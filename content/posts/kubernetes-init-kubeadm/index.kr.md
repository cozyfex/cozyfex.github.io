---
title: "Kubernetes Init Kubeadm"

date: 2021-06-04T22:34:05+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- kubeadm

tags:

- kubernetes
- cluster
- kubeadm

keywords:

- kubernetes
- cluster
- kubeadm

---

## Master Node Init

### Single Master Node Init

```shell
sudo kubeadm init
```

### Multi Master Node Init

```shell
sudo kubeadm init --control-plane-endpoint "10.0.2.100:1644" --upload-certs
```

## Copy Admin Config

```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Install `weaveworks` Addon as CNI

```shell
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

## Join Master Nodes

```shell
sudo kubeadm join 10.0.2.100:1644 \
  --token 1i8jwf.tj8h3nxlkwtmvj1u \
  --discovery-token-ca-cert-hash sha256:6e07e474c9264985bfa3ad26009c5036b2909cb1aac2115126e398800c195939 \
  --control-plane \
  --certificate-key 87d0c518e9a9ed6ddc33e7f527432eb5db03a429954586d481da46a3ef9d3afb
```

## Join Worker Nodes

```shell
sudo kubeadm join 10.0.2.100:1644 \
  --token 1i8jwf.tj8h3nxlkwtmvj1u \
  --discovery-token-ca-cert-hash sha256:6e07e474c9264985bfa3ad26009c5036b2909cb1aac2115126e398800c195939
```

## Print Join Command

```shell
kubeadm token create --print-join-command
```

