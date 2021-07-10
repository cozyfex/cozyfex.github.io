---
title: "Kubernetes POD 간의 보안 통신을 위한 mTLS"

date: 2021-07-08T17:18:52+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- mtls
- service mesh

tags:

- kubernetes
- k8s
- orchestration
- mtls
- service mesh
- envoy
- istio
- linkerd

keywords:

- kubernetes
- k8s
- orchestration
- mtls
- service mesh
- envoy
- istio
- linkerd

---

## mTLS(Mutual Transport Layer Security)

{{<admonition note mTLS true>}}  
일반적으로 거의 웹 서비스는 단방향 TLS를 사용합니다.  
`mTLS`는 서버와 클라이언트 모두 인증을 위한 것입니다.  
`Mutual Authentication` 방법 중 하나입니다.  
Kubernetes 에서 `mTLS`는 POD 간의 통신에 사용됩니다.  
{{</admonition>}}

## Sidecar Pattern

{{<admonition note "Sidecar Pattern" true>}}  
컨테이너가 있다고 가정하고 이를 `AppContainer`라고 하자.  
`AppContainer`는 애플리케이션 전용입니다.  
`AppContainer`에는 로그, 모니터 등과 같은 다른 프로세스가 없습니다.  
이와 같은 프로세스를 추가하려는 경우 실행 중인 애플리케이션에 영향을 줍니다.  
이 시점에서 프로세스에 대한 다른 컨테이너를 추가할 수 있습니다.  
이 패턴을 `Sidecar Pattern`이라고 합니다.  
그리고 이 경우 이를 `SidecarContainers`라고 합니다.  
`SidecarContainers`는 `AppContainer`와 독립적입니다.  
`AppContainer`는 `SidecarContainers`와도 독립적입니다.  
`SidecarContainers`와 `AppContainer`는 서로 영향을 미치지 않습니다.  
{{</admonition>}}

## Service Mesh

{{<admonition note "Service Mesh" true>}}  
[WIKIPEDIA](https://en.wikipedia.org/wiki/Service_mesh)에 따르면 소프트웨어 아키텍처에서 서비스 메시는 프록시를 사용하여 서비스 또는 마이크로 서비스 간의 서비스 간 통신을 용이하게 하기위한 전용 인프라 계층입니다.  
{{</admonition>}}

## Service Mesh Solutions

* [LINKERD](https://linkerd.io)
* [Istio](https://istio.io/latest/about/service-mesh/) & [envoy](https://www.envoyproxy.io)

## Service Mesh in Kubernetes

{{<admonition note "Service Mesh" true>}}  
Kubernetes는 mTLS에 Service Mesh를 사용하고 있습니다.  
이것은 Service Mesh 의 한 사용법입니다.  
{{</admonition>}}

### Basic

{{<image src="kubernetes-service-mesh-basic.png" width="100%">}}

### Permissive / Opportunistic

{{<image src="kubernetes-service-mesh-permissive.png" width="100%">}}

### Enforced / Strict

{{<image src="kubernetes-service-mesh-enforced.png" width="100%">}}

