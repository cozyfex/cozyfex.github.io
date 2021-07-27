---
title: "Kubernetes Secret 과 ETCD"

date: 2021-07-27T20:22:46+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- secret
- etcd

keywords:

- kubernetes
- secret
- etcd

---

## 인증 파일들 확인

```shell
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep etcd
```

## ETCD 로 Secret 정보 얻기

{{<admonition note ETCD true>}}  
In the `get`, you have to check the path.  
`/registry/<resource>/<namespace>/<resource-name>`
`/registry/secrets/team-ns/database-access`
{{</admonition>}}

```shell
ETCDCTL_API=3 etcdctl \
                --cert /etc/kubernetes/pki/apiserver-etcd-client.crt \
                --key /etc/kubernetes/pki/apiserver-etcd-client.key \
                --cacert /etc/kubernetes/pki/etcd/ca.crt \
                get /registry/secrets/team-ns/database-access
```


