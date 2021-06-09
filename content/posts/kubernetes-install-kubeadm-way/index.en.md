---
title: "Kubernetes Install kubeadm Way"

date: 2021-06-02T21:33:16+09:00

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

## Prerequisites

### swap

#### Show swap

```shell
free
```

```shell
sudo swapon -show
```

#### Off swap

```shell
sudo swapoff -a
```

#### Disable swap

```shell
sudo vi /etc/fstab
```

```
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-9P6wxgVviPOldHd8McXwnECNFYGbJQXIcJX3JQbySwOeEHhF09z1mRNWyWSVb8jb / ext4 defaults 0 0
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/5afd959f-a436-4e5e-8ae5-456f52e6cffe /boot ext4 defaults 0 0
# Below change to comment!!
#/swap.img      none    swap    sw      0       0
```

### Enable Bridged Traffic

#### Check Loaded

```shell
lsmod | grep br_netfilter
```

#### Load Explicitly

```shell
sudo modprobe br_netfilter
```

#### Ensure `net.bridge.bridge-nf-call-iptables`

```shell
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```

### Ubuntu Firewall

<sub>Control-plane nodes</sub>

```shell
sudo ufw allow 6443/tcp
sudo ufw allow 2379:2380/tcp
sudo ufw allow 10250/tcp
sudo ufw allow 10251/tcp
sudo ufw allow 10252/tcp
```

<sub>Worker nodes</sub>

```shell
sudo ufw allow 10250/tcp
sudo ufw allow 30000:32767/tcp
```

### Install Docker

```shell
sudo apt install docker.io
```

#### Cgroup Driver

```shell
sudo mkdir /etc/docker
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
```

```shell
sudo systemctl enable docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## Install `kubeadm`

### Update the apt Package Index and Install Packages Needed to Use the Kubernetes apt repository

```shell
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```

### Download the Google Cloud Public Signing Key

```shell
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```

### Add the Kubernetes apt repository

```shell
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

### Update apt Package Index, Install `kubelet`, `kubeadm` and `kubectl`, and Pin Their Version

```shell
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

## Initialize `kubeadm`

```shell
kubeadm init
```

## Token

### Token List

```shell
kubeadm token list
```

### Create a Token

```shell
kubeadm token create --print-join-command
```

## Troubleshooting

### strace

{{<admonition note Troubleshooting true>}}  
If you got a below message when you run `kubectl version`, follow this section.

```
The connection to the server 10.0.0.68:6443 was refused - did you specify the right host or port?
```

{{</admonition>}}

```shell
strace -eopenat kubectl version
```



