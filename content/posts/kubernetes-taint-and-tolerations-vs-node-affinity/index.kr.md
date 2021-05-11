---
title: "Kubernetes Taint and Tolerations vs Node Affinity"

date: 2021-05-11T19:01:24+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- scheduler
- taint
- tolerations
- affinity

keywords:

- kubernetes
- scheduler
- taint
- tolerations
- affinity

---

## Taint 와 Tolerations

{{<admonition note Limit true>}}  
`POD` 들을 특정 Node 에 스케줄링 하지 않게 할 수 있지만, `POD` 이 특정 Node 에 항상 스케줄링 하게 할 수는 없다.  
{{</admonition>}}

## Node Affinity

{{<admonition note Limit true>}}  
`POD` 들을 특정 Node 에 스케줄링 하거나, 스케줄링 안되게 할 수 있다. 그러나 스케줄링을 피하게 할 수는 없다.  
{{</admonition>}}

## Combination Taint/Tolerations and Node Affinity

{{<admonition note "The Best Solution" true>}}  
이 두가지 방법을 잘 사용하면 대부분의 스케줄링 문제를 해결 할 수 있다.  
{{</admonition>}}
