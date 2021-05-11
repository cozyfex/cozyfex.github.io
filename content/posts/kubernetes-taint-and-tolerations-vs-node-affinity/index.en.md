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

## Taint and Tolerations

{{<admonition note Limit true>}}  
You can make a rule that does not schedule the `POD`s to the specific Node, but you can not make a rule that schedules always the `POD`s to the specific Node.  
{{</admonition>}}

## Node Affinity

{{<admonition note Limit true>}}  
You can make a rule that schedules the `POD`s to the specific Node or does not schedule the `POD`s to the specific Node, but you cannot make a rule that avoids to schedule to the specific Node.  
{{</admonition>}}

## Combination Taint/Tolerations and Node Affinity

{{<admonition note "The Best Solution" true>}}  
Using this combination, you can solve any problem that schedule the `POD`s.  
{{</admonition>}}
