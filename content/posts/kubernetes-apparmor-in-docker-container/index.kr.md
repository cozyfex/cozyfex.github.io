---
title: "Kubernetes Docker Container 에서 AppArmor"

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

## POD 이 실행중인 Node 에 접속

```shell
ssh node01
```

## Container ID 확인

```shell
docker ps -a | grep apparmor
```

## Container 조사

```shell
docker inspect 41f014a9e7a8 | grep -i profile
```

```
...
"AppArmorProfile": "very-secure",
...
```

