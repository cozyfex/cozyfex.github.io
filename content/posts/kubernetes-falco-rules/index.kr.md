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

## Falco 디렉토리로 이동

```shell
cd /etc/falco
```

## Rule 찾기

```shell
grep -r "Package management process launched" .
```

## Backup

```shell
cp falco_rules.yaml falco_rules.yaml.bak
```

## Local Rule 에 적용

```shell
vi falco_rules.local.yaml
```
