---
title: "Kubernetes API Groups"

date: 2021-05-24T15:43:12+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- api
- api groups

keywords:

- kubernetes
- security
- api
- api groups

---

## URL 스키마

### Version

{{<admonition note Version true>}}  
curl https://kube-master:6443`/version`
{{</admonition>}}

### API

{{<admonition note Version true>}}  
curl https://kube-master:6443`/api/`v1/pods  
{{</admonition>}}

## Groups

### `/metrics`

{{<admonition note "/metrics" true>}}

{{</admonition>}}

### `/healthz`

{{<admonition note "/healthz" true>}}

{{</admonition>}}

### `/version`

{{<admonition note "/version" true>}}

{{</admonition>}}

### `/api`

{{<admonition note "/api" true>}}

{{</admonition>}}

### `/apis`

{{<admonition note "/apis" true>}}

{{</admonition>}}

### `/logs`

{{<admonition note "/logs" true>}}

{{</admonition>}}

## Core

{{<admonition note "core" true>}}  
Core(레거시라고도 불리는) 그룹은 REST `/api/v1` 경로에서 볼 수 있다.  
Core 그룹은 apiVersion 필드에서 특별하게 지정되지 않는다.(예: `apiVersion: v1).  
{{</admonition>}}

{{<mermaid>}}

graph LR;

A(/API) --> V(/v1)

V --> N(namespaces)

V --> P(pods)

V --> R(rc - replicaset)

V --> E(events)

V --> EP(endpoints)

V --> ND(nodes)

V --> B(bindings)

V --> PV(PV - Persistent Volume)

V --> PVC(PVC)

V --> C(configmaps)

V --> S(secrets)

V --> SV(services)

{{</mermaid>}}

## Named

{{<admonition note "named" true>}}  
Named 그룹은 REST `/apis/$GROUP_NAME/$VERSION` 경로에 있고, `apiVersion: $GROUP_NAME/$VERSION` 같이 사용한다.  
모든 API 그룹의 목록은 [Kubernetes API reference](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.21/#-strong-api-groups-strong-)에서 확인 가능하다.  
{{</admonition>}}

{{<mermaid>}}

graph LR;

A(/apis) --> AP(/apps)

A --> E(/extensions)

A --> N(/networking.k8s.io)

A --> S(storage.k8s.io)

A --> AU(/authentication.k8s.io)

A --> C(certificates.k8s.io)

{{</mermaid>}}

### `/apps`

{{<mermaid>}}

graph TD;

A(/apps) --> V(/v1)

V --> D(/deployments)

V --> R(/replicasets)

V --> S(/statefulsets)

{{</mermaid>}}

#### Resources

##### `/deployments`

{{<mermaid>}}

graph TD;

M(/deployments) --> L(list)  
M --> G(get)  
M --> C(create)  
M --> D(delete)  
M --> U(update)  
M --> W(watch)

{{</mermaid>}}

##### `/replicasets`

{{<mermaid>}}

graph TD;

M(/deployments) --> L(list)  
M --> G(get)  
M --> C(create)  
M --> D(delete)  
M --> U(update)  
M --> W(watch)

{{</mermaid>}}

##### `/statefulsets`

{{<mermaid>}}

graph TD;

M(/deployments) --> L(list)  
M --> G(get)  
M --> C(create)  
M --> D(delete)  
M --> U(update)  
M --> W(watch)

{{</mermaid>}}

### `/networking.k8s.io`

{{<mermaid>}}

graph TD;

N(/networking.k8s.io) --> V(/v1)  
V --> NP(/networkpolicies)

{{</mermaid>}}

### `/certificates.k8s.io`

{{<mermaid>}}

graph TD;

C(/certificates.k8s.io) --> V(/v1)  
V --> CSR(/certificatesigningrequests)

{{</mermaid>}}

## API 호출

### API Server 실행

{{<admonition note "minikube" true>}}  
이 섹션에서는 `minikube`를 사용한다.  
아래와 같이 확인 하기를 원한다면, `minikube` 를 설치 해야만 한다.  
{{</admonition>}}

```shell
kubectl proxy
```

```shell
Starting to serve on 127.0.0.1:8001
```

### 모든 API 목록

```shell
curl http://127.0.0.1:8001 -k
```

```shell
{
  "paths": [
    "/.well-known/openid-configuration",
    "/api",
    "/api/v1",
    "/apis",
    "/apis/",
    "/apis/admissionregistration.k8s.io",
    "/apis/admissionregistration.k8s.io/v1",
    "/apis/admissionregistration.k8s.io/v1beta1",
    "/apis/apiextensions.k8s.io",
    "/apis/apiextensions.k8s.io/v1",
    "/apis/apiextensions.k8s.io/v1beta1",
    "/apis/apiregistration.k8s.io",
    "/apis/apiregistration.k8s.io/v1",
    "/apis/apiregistration.k8s.io/v1beta1",
    "/apis/apps",
    "/apis/apps/v1",
    "/apis/authentication.k8s.io",
    "/apis/authentication.k8s.io/v1",
    "/apis/authentication.k8s.io/v1beta1",
    "/apis/authorization.k8s.io",
    "/apis/authorization.k8s.io/v1",
    "/apis/authorization.k8s.io/v1beta1",
    "/apis/autoscaling",
    "/apis/autoscaling/v1",
    "/apis/autoscaling/v2beta1",
    "/apis/autoscaling/v2beta2",
    "/apis/batch",
    "/apis/batch/v1",
    "/apis/batch/v1beta1",
    "/apis/certificates.k8s.io",
    "/apis/certificates.k8s.io/v1",
    "/apis/certificates.k8s.io/v1beta1",
    "/apis/coordination.k8s.io",
    "/apis/coordination.k8s.io/v1",
    "/apis/coordination.k8s.io/v1beta1",
    "/apis/discovery.k8s.io",
    "/apis/discovery.k8s.io/v1beta1",
    "/apis/events.k8s.io",
    "/apis/events.k8s.io/v1",
    "/apis/events.k8s.io/v1beta1",
    "/apis/extensions",
    "/apis/extensions/v1beta1",
    "/apis/flowcontrol.apiserver.k8s.io",
    "/apis/flowcontrol.apiserver.k8s.io/v1beta1",
    "/apis/networking.k8s.io",
    "/apis/networking.k8s.io/v1",
    "/apis/networking.k8s.io/v1beta1",
    "/apis/node.k8s.io",
    "/apis/node.k8s.io/v1",
    "/apis/node.k8s.io/v1beta1",
    "/apis/policy",
    "/apis/policy/v1beta1",
    "/apis/rbac.authorization.k8s.io",
    "/apis/rbac.authorization.k8s.io/v1",
    "/apis/rbac.authorization.k8s.io/v1beta1",
    "/apis/scheduling.k8s.io",
    "/apis/scheduling.k8s.io/v1",
    "/apis/scheduling.k8s.io/v1beta1",
    "/apis/storage.k8s.io",
    "/apis/storage.k8s.io/v1",
    "/apis/storage.k8s.io/v1beta1",
    "/healthz",
    "/healthz/autoregister-completion",
    "/healthz/etcd",
    "/healthz/log",
    "/healthz/ping",
    "/healthz/poststarthook/aggregator-reload-proxy-client-cert",
    "/healthz/poststarthook/apiservice-openapi-controller",
    "/healthz/poststarthook/apiservice-registration-controller",
    "/healthz/poststarthook/apiservice-status-available-controller",
    "/healthz/poststarthook/bootstrap-controller",
    "/healthz/poststarthook/crd-informer-synced",
    "/healthz/poststarthook/generic-apiserver-start-informers",
    "/healthz/poststarthook/kube-apiserver-autoregistration",
    "/healthz/poststarthook/priority-and-fairness-config-consumer",
    "/healthz/poststarthook/priority-and-fairness-config-producer",
    "/healthz/poststarthook/priority-and-fairness-filter",
    "/healthz/poststarthook/rbac/bootstrap-roles",
    "/healthz/poststarthook/scheduling/bootstrap-system-priority-classes",
    "/healthz/poststarthook/start-apiextensions-controllers",
    "/healthz/poststarthook/start-apiextensions-informers",
    "/healthz/poststarthook/start-cluster-authentication-info-controller",
    "/healthz/poststarthook/start-kube-aggregator-informers",
    "/healthz/poststarthook/start-kube-apiserver-admission-initializer",
    "/livez",
    "/livez/autoregister-completion",
    "/livez/etcd",
    "/livez/log",
    "/livez/ping",
    "/livez/poststarthook/aggregator-reload-proxy-client-cert",
    "/livez/poststarthook/apiservice-openapi-controller",
    "/livez/poststarthook/apiservice-registration-controller",
    "/livez/poststarthook/apiservice-status-available-controller",
    "/livez/poststarthook/bootstrap-controller",
    "/livez/poststarthook/crd-informer-synced",
    "/livez/poststarthook/generic-apiserver-start-informers",
    "/livez/poststarthook/kube-apiserver-autoregistration",
    "/livez/poststarthook/priority-and-fairness-config-consumer",
    "/livez/poststarthook/priority-and-fairness-config-producer",
    "/livez/poststarthook/priority-and-fairness-filter",
    "/livez/poststarthook/rbac/bootstrap-roles",
    "/livez/poststarthook/scheduling/bootstrap-system-priority-classes",
    "/livez/poststarthook/start-apiextensions-controllers",
    "/livez/poststarthook/start-apiextensions-informers",
    "/livez/poststarthook/start-cluster-authentication-info-controller",
    "/livez/poststarthook/start-kube-aggregator-informers",
    "/livez/poststarthook/start-kube-apiserver-admission-initializer",
    "/logs",
    "/metrics",
    "/openapi/v2",
    "/openid/v1/jwks",
    "/readyz",
    "/readyz/autoregister-completion",
    "/readyz/etcd",
    "/readyz/informer-sync",
    "/readyz/log",
    "/readyz/ping",
    "/readyz/poststarthook/aggregator-reload-proxy-client-cert",
    "/readyz/poststarthook/apiservice-openapi-controller",
    "/readyz/poststarthook/apiservice-registration-controller",
    "/readyz/poststarthook/apiservice-status-available-controller",
    "/readyz/poststarthook/bootstrap-controller",
    "/readyz/poststarthook/crd-informer-synced",
    "/readyz/poststarthook/generic-apiserver-start-informers",
    "/readyz/poststarthook/kube-apiserver-autoregistration",
    "/readyz/poststarthook/priority-and-fairness-config-consumer",
    "/readyz/poststarthook/priority-and-fairness-config-producer",
    "/readyz/poststarthook/priority-and-fairness-filter",
    "/readyz/poststarthook/rbac/bootstrap-roles",
    "/readyz/poststarthook/scheduling/bootstrap-system-priority-classes",
    "/readyz/poststarthook/start-apiextensions-controllers",
    "/readyz/poststarthook/start-apiextensions-informers",
    "/readyz/poststarthook/start-cluster-authentication-info-controller",
    "/readyz/poststarthook/start-kube-aggregator-informers",
    "/readyz/poststarthook/start-kube-apiserver-admission-initializer",
    "/readyz/shutdown",
    "/version"
  ]
}
```

### API 정보

```shell
curl http://127.0.0.1:8001/api -k
```

```shell
{
  "kind": "APIVersions",
  "versions": [
    "v1"
  ],
  "serverAddressByClientCIDRs": [
    {
      "clientCIDR": "0.0.0.0/0",
      "serverAddress": "192.168.49.2:8443"
    }
  ]
}
```

### API 그룹

```shell
curl http://127.0.0.1:8001/apis -k
```

```shell
{
  "kind": "APIGroupList",
  "apiVersion": "v1",
  "groups": [
    {
      "name": "apiregistration.k8s.io",
      "versions": [
        {
          "groupVersion": "apiregistration.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "apiregistration.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "apiregistration.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "apps",
      "versions": [
        {
          "groupVersion": "apps/v1",
          "version": "v1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "apps/v1",
        "version": "v1"
      }
    },
    {
      "name": "events.k8s.io",
      "versions": [
        {
          "groupVersion": "events.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "events.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "events.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "authentication.k8s.io",
      "versions": [
        {
          "groupVersion": "authentication.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "authentication.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "authentication.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "authorization.k8s.io",
      "versions": [
        {
          "groupVersion": "authorization.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "authorization.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "authorization.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "autoscaling",
      "versions": [
        {
          "groupVersion": "autoscaling/v1",
          "version": "v1"
        },
        {
          "groupVersion": "autoscaling/v2beta1",
          "version": "v2beta1"
        },
        {
          "groupVersion": "autoscaling/v2beta2",
          "version": "v2beta2"
        }
      ],
      "preferredVersion": {
        "groupVersion": "autoscaling/v1",
        "version": "v1"
      }
    },
    {
      "name": "batch",
      "versions": [
        {
          "groupVersion": "batch/v1",
          "version": "v1"
        },
        {
          "groupVersion": "batch/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "batch/v1",
        "version": "v1"
      }
    },
    {
      "name": "certificates.k8s.io",
      "versions": [
        {
          "groupVersion": "certificates.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "certificates.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "certificates.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "networking.k8s.io",
      "versions": [
        {
          "groupVersion": "networking.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "networking.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "networking.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "extensions",
      "versions": [
        {
          "groupVersion": "extensions/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "extensions/v1beta1",
        "version": "v1beta1"
      }
    },
    {
      "name": "policy",
      "versions": [
        {
          "groupVersion": "policy/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "policy/v1beta1",
        "version": "v1beta1"
      }
    },
    {
      "name": "rbac.authorization.k8s.io",
      "versions": [
        {
          "groupVersion": "rbac.authorization.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "rbac.authorization.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "rbac.authorization.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "storage.k8s.io",
      "versions": [
        {
          "groupVersion": "storage.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "storage.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "storage.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "admissionregistration.k8s.io",
      "versions": [
        {
          "groupVersion": "admissionregistration.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "admissionregistration.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "admissionregistration.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "apiextensions.k8s.io",
      "versions": [
        {
          "groupVersion": "apiextensions.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "apiextensions.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "apiextensions.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "scheduling.k8s.io",
      "versions": [
        {
          "groupVersion": "scheduling.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "scheduling.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "scheduling.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "coordination.k8s.io",
      "versions": [
        {
          "groupVersion": "coordination.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "coordination.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "coordination.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "node.k8s.io",
      "versions": [
        {
          "groupVersion": "node.k8s.io/v1",
          "version": "v1"
        },
        {
          "groupVersion": "node.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "node.k8s.io/v1",
        "version": "v1"
      }
    },
    {
      "name": "discovery.k8s.io",
      "versions": [
        {
          "groupVersion": "discovery.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "discovery.k8s.io/v1beta1",
        "version": "v1beta1"
      }
    },
    {
      "name": "flowcontrol.apiserver.k8s.io",
      "versions": [
        {
          "groupVersion": "flowcontrol.apiserver.k8s.io/v1beta1",
          "version": "v1beta1"
        }
      ],
      "preferredVersion": {
        "groupVersion": "flowcontrol.apiserver.k8s.io/v1beta1",
        "version": "v1beta1"
      }
    }
  ]
}
```

## Kube Proxy 은 KubeCtl Proxy 와 다르다.

