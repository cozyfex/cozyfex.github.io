---
title: "Kubernetes Benchmark"

date: 2021-07-02T14:47:15+09:00

categories:

- kubernetes
- k8s
- orchestration
- benchmark

tags:

- kubernetes
- benchmark

keywords:

- kubernetes
- benchmark

---

## CIS(Center for Internet Security) Benchmark

{{<admonition note "CIS-CAT Pro" true>}}  
CIS-CAT Pro is supporting Kubernetes assessment.  
{{</admonition>}}

```shell
./Assessor-CLI.sh -i -nts -rd /var/www/html -rp index
```

## Kube-Bench of aqua

{{<admonition note "Kube-Bench" true>}}  
[kube-bench](https://github.com/aquasecurity/kube-bench) is tool that checks whether Kubernetes is deployed securely by running the checks documented in the [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes/).  
{{</admonition>}}

### Installation

{{<admonition note "Installation" true>}}  
You can check [Installation](https://github.com/aquasecurity/kube-bench/blob/main/docs/installation.md) document.  
{{</admonition>}}

```shell
 ./kube-bench --config-dir `pwd`/cfg --config `pwd`/cfg/config.yaml 
```
