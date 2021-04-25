---
title: "Kubernetes Structure"

date: 2021-04-25T16:44:32+09:00

author: CozyFex

categories:

- kubernetes

tags:

- kubernetes
- orchestration
- container

keywords:

- kubernetes
- orchestration
- container

---

## Kubernetes Structure

> *Here's explanation of Kubernetes structure that I understand.\
So, it could be wrong.\
I just record for memory.*

### Cluster

A computing set they have a same purpose.\
In Kubernetes, there are `Master` and `Node`.\
같은 목적을 가진 컴퓨팅 집합.\
쿠버네티스에는 `Master`와 `Node`로 구분된다.

### Master

A controller for managing the `Cluster`.\
클러스터 전체를 관리하는 컨트롤러.

### Node

This is a physical machine that is for deploying containers.\
컨테이너를 배포하는 물리적인 머신.

### Pod

The `Pod` is a deployment unit in Kubernetes.\
A `Pod` has multiple containers.\
The Kubernetes is managing this unit.\
In a `Node` can have multiple `Pod`.\
쿠버네티스에서 배포하는 단위를 `Pod`이라 한다.\
`Pod`은 여러개의 컨테이너의 묶음이다.\
쿠버네티스가 관리하는 단위.\
하나의 `Node`에는 여러개의 `Pod`이 있을 수 있다.
> #### Reason why Kubernetes manage by unit `Pod`
> - In a `Pod` is sharing IP and Port.
> - In a `Pod` can share disk volumes.
> #### 쿠버네티스가 `Pod` 단위로 관리하는 이유
> - 같은 `Pod`은 IP와 Port를 공유한다.
> - 같은 `Pod`은 디스크 볼륨을 공유할 수 있다.

### Container

This is a technology for virtualization based on linux.\
리눅스를 기반한 가상화 기술.

