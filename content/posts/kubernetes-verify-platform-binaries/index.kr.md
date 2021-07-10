---
title: "Kubernetes Platform 바이너리 검증"

date: 2021-07-04T17:47:21+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- verify
- platform
- binaries

keywords:

- kubernetes
- verify
- platform
- binaries

---

## Verify

{{<admonition note "Download" true>}}  
[Releases](https://github.com/kubernetes/kubernetes/releases) 에서 다운로드  
{{</admonition>}}

```shell
wget -O kubernetes.tar.gz https://github.com/kubernetes/kubernetes/archive/refs/tags/v1.21.2.tar.gz

curl https://github.com/kubernetes/kubernetes/archive/refs/tags/v1.21.2.tar.gz -L -o kubernetes.tar.gz
```

## SHA 생성

### MacOS

```shell
shasum -a 512 kubernetes.tar.gz
```

### Linux

```shell
sha512sum kubernetes.tar.gz
```

## Released SHA512 와 비교

{{<admonition note "Download" true>}}  
[Releases Notes](https://kubernetes.io/releases/notes/) 에서, SHA512 비교할 수 있다.  
{{</admonition>}}