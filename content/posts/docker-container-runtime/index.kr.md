---
title: "Docker Container Runtime"

date: 2021-07-08T00:51:55+09:00

author: CozyFex

categories:

- docker
- container runtime

tags:

- docker
- container runtime

keywords:

- docker
- container runtime

---

## Docker Container Runtime

{{<mermaid>}}

graph TD;

DC(Docker CLI) --> RA(REST API)

RA --> DD(Docker Daemon<br><br>Images Volumes Networks)

DD --> CD(containerd<br><br>Manage Containers)

CD --> CS(containerd-shim)

CS --> RC(runC<br><br>Run containers<br><br>docker run -d nginx)

CS --> KT(Kata<br><br>kata-runtime<br><br>docker run --runtime kata -d nginx)

CS --> GV(gVisor<br><br>runsc<br><br>docker run --runtime runsc -d nginx)

{{</mermaid>}}

## runC

```shell
docker run -d nginx
```

## Kata Containers

```shell
docker run --runtime kata -d nginx
```

## gVisor

```shell
docekr run --runtime runsc -d nginx
```