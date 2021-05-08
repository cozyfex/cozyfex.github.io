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

{{<admonition note>}}  
여기의 쿠버네티스 구조 설명은 내가 이해한것을 기반으로 한 것이다.  
그러므로 틀릴 수도 있음을 알린다.  
그냥 내가 기억을 위해 기록한 것이다.  
{{</admonition>}}

## Cluster

같은 목적을 가진 컴퓨팅 집합.\
쿠버네티스에는 `Master`와 `Node`로 구분된다.

## Master

클러스터 전체를 관리하는 컨트롤러.

## Node

컨테이너를 배포하는 물리적인 머신.

## POD

쿠버네티스에서 배포하는 단위를 `POD`이라 한다.\
`POD`은 여러개의 컨테이너의 묶음이다.\
쿠버네티스가 관리하는 단위.\
하나의 `Node`에는 여러개의 `POD`이 있을 수 있다.

{{<admonition note "쿠버네티스가 `POD` 단위로 관리하는 이유" true>}}  
같은 `POD`은 IP와 Port를 공유한다.  
같은 `POD`은 디스크 볼륨을 공유할 수 있다.  
{{</admonition>}}

## Container

리눅스를 기반한 가상화 기술.

