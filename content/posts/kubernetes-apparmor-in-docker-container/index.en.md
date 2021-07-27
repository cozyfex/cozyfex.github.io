---
title: "Kubernetes AppArmor in Docker Container"

date: 2021-07-27T20:12:07+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- apparmor
- docker
- container

tags:

- kubernetes
- apparmor
- docker
- container

keywords:

- kubernetes
- apparmor
- docker
- container

---

## Connect to the Node that is Running the POD

```shell
ssh node01
```

## Find the Container ID

```shell
docker ps -a | grep apparmor
```

## Inspect the Container

```shell
docker inspect 41f014a9e7a8 | grep -i profile
```

```
...
"AppArmorProfile": "very-secure",
...
```

