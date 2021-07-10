---
title: "Kubernetes mTLS to Secure Communication Between PODS"

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
Typically, almost web services are using one way TLS.  
`mTLS` is for authentication both server and client.  
It's one of the way `Mutual Authentication`
In Kubernetes `mTLS` is using for communicating between PODs.  
{{</admonition>}}

## Sidecar Pattern

{{<admonition note "Sidecar Pattern" true>}}  
Let's assume there's a container, and we call it `AppContainer`.  
The `AppContainer` is for only application.  
In `AppContainer` doesn't have any other process like log, monitor, etc.  
If you want to add process like that, it affects to application running.  
At this point, we can add other containers for the processes.  
This pattern is calling `Sidecar Pattern`.  
And in this case, we call it `SidecarContainers`.  
The `SidecarContainers` is independent of `AppContainer`.  
The `AppContainer` is also independent of `SidecarContainers`.  
The `SidecarContainers` and `AppContainer` don't affect each others.  
{{</admonition>}}

## Service Mesh

{{<admonition note "Service Mesh" true>}}  
According to [WIKIPEDIA](https://en.wikipedia.org/wiki/Service_mesh), in software architecture, a service mesh is a dedicated infrastructure layer for facilitating service-to-service communications between services or microservices, using a proxy.  
{{</admonition>}}

## Service Mesh Solutions

* [LINKERD](https://linkerd.io)
* [Istio](https://istio.io/latest/about/service-mesh/) & [envoy](https://www.envoyproxy.io)

## Service Mesh in Kubernetes

{{<admonition note "Service Mesh" true>}}  
The Kubernetes is using Service Mesh for mTLS.  
It is just one usage of Service Mesh.  
{{</admonition>}}

### Basic

{{<image src="kubernetes-service-mesh-basic.png" width="100%">}}

### Permissive / Opportunistic

{{<image src="kubernetes-service-mesh-permissive.png" width="100%">}}

### Enforced / Strict

{{<image src="kubernetes-service-mesh-enforced.png" width="100%">}}

