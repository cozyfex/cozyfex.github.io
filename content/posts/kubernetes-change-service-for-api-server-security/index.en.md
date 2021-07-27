---
title: "Kubernetes Change Service for API Server Security"

date: 2021-07-27T19:35:33+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- security
- api server

tags:

- kubernetes
- security
- api server
- change service

keywords:

- kubernetes
- security
- api server
- change service

---

## Check the Service

```shell
ps aux | grep kube-apiserver
```

{{<admonition note "--kubernetes-service-node-port" true>}}  
You can see `--kubernetes-service-node-port=31000`  
{{</admonition>}}

## Check Exist Services

```shell
kubectl get svc
```

```
NAME         TYPE       CLUSTER-IP   EXTERNAL-IP   PORT(S)         AGE
kubernetes   NodePort   10.96.0.1    <none>        443:31000/TCP   5d3h
```

## Backup

```shell
cp /etc/kubernetes/manifests/kube-apiserver.yaml ~/
```

## Edit `/etc/kubernetes/manifests/kube-apiserver.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 192.168.100.11:6443
  creationTimestamp: null
  labels:
    component: kube-apiserver
    tier: control-plane
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
    - command:
        - kube-apiserver
        - --advertise-address=192.168.100.11
        - --allow-privileged=true
        - --authorization-mode=Node,RBAC
        - --client-ca-file=/etc/kubernetes/pki/ca.crt
        - --enable-admission-plugins=NodeRestriction
        - --enable-bootstrap-token-auth=true
        - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
        - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
        - --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
        - --etcd-servers=https://127.0.0.1:2379
        - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
        - --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        #    - --kubernetes-service-node-port=31000   # delete or set to 0
        - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
        - --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
...
```

## Delete the Service

```shell
kubectl delete svc kubernetes
```



