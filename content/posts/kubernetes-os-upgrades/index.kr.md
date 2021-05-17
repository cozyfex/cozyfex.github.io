---
title: "Kubernetes 운영체제 업그레이드"

date: 2021-05-17T02:26:09+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- cluster maintenance
- os upgrade

keywords:

- kubernetes
- cluster maintenance
- os upgrade

---

## Node 가 오프라인일 때

{{<admonition note Note true>}}  
쿠버네티스는 컨트롤러 메니저에 설정된 5분을 기다린다.  
5분 이후에도 노드가 오프라인이면, 쿠버네티스는 다른 노드들에 `POD` 들을 생성한다.  
그리고 나서 노드가 온라인이 되더라도, 그 노드는 비어있다.  
{{</admonition>}}

### 축출 시간 설정

```shell
kube-controller-manager --pod-eviction-timeout=5m0s
```

## cordon Node

{{<admonition note cordon true>}}  
`cordon` 은 해당 노드에 어떤 `POD` 도 스케줄링 되지 않게 한다.  
노드에 스케줄이 되지 않게 마크한다.  
`cordon` 한 후에는, `describe` 를 사용 할 수 없다.  
{{</admonition>}}

```shell
# Structure
kubectl cordon <node-name>

# Example
kubectl cordon node01
```

## uncordon Node

{{<admonition note uncordon true>}}  
`uncordon` 은 해당 노드에 다시 `POD` 이 스케줄링 되게 한다.  
노드에 스케줄링이 되게 마크한다.  
{{</admonition>}}

```shell
# Structure
kubectl uncordon <node-name>

# Example
kubectl uncordon node01
```

## drain Node

{{<admonition note drain true>}}  
`drain` 은 `POD` 들을 다른 노드들로 옮긴다.  
`drain` 은 `cordon` 도 함께 실행한다.   
{{</admonition>}}

```shell
# Structure
kubectl drain <node-name>

# Example
kubectl drain node01
```

## 운영체제 업그레이드 프로세스

{{<mermaid>}}  
stateDiagram  
[*] --> drain  
drain --> Upgrade  
Upgrade --> uncordon  
uncordon --> [*]  
{{</mermaid>}}
