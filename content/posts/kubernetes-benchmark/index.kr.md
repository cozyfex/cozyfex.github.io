---
title: "Kubernetes Benchmark"

date: 2021-07-02T14:47:15+09:00

author: CozyFex

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
CIS-CAT Pro 는 Kubernetes 평가를 지원합니다.  
{{</admonition>}}

```shell
./Assessor-CLI.sh -i -nts -rd /var/www/html -rp index
```

## aqua 의 Kube-Bench

{{<admonition note "Kube-Bench" true>}}  
[kube-bench](https://github.com/aquasecurity/kube-bench) 는 [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes/) 문서화된 검사를 실행하여 Kubernetes 가 안전하게 배포되었는지 확인하는 도구입니다.  
{{</admonition>}}

### Installation

{{<admonition note "Installation" true>}}  
공식 문서 [Installation](https://github.com/aquasecurity/kube-bench/blob/main/docs/installation.md)
{{</admonition>}}

```shell
 ./kube-bench --config-dir `pwd`/cfg --config `pwd`/cfg/config.yaml 
```
