---
title: "Kubernetes Falco Rules"

date: 2021-07-27T19:28:28+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- falco

tags:

- kubernetes
- falco
- rules

keywords:

- kubernetes
- falco
- rules

---

## Move to Falco Directory

```shell
cd /etc/falco
```

## Find the Rule

```shell
grep -r "Package management process launched" .
```

## Backup

```shell
cp falco_rules.yaml falco_rules.yaml.bak
```

## Apply to Local Rule

```shell
vi falco_rules.local.yaml
```
