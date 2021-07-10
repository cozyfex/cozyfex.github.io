---
title: "Kubernetes OPA"

date: 2021-07-06T20:49:18+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- opa
- open policy agent

tags:

- kubernetes
- opa
- open policy agent

keywords:

- kubernetes
- opa
- open policy agent

---

## Kubernetes 에서의 OPA

{{<admonition note "OPA in Kubernetes" true>}}  
Kubernetes 에서의 OPA 는 Admission Controllers 중 하나인 `ValidatingAdmissionWebhook` 를 이용한다.  
{{</admonition>}}

## OPA 를 위한 ConfigMap 생성

### `untrusted-registry.rego`

```rego
package kubernetes.admission

deny[msg] {
  input.request.kind.kind == "Pod"
  image := input.request.object.spec.containers[_].image
  not startswith(image, "hooli.com/")
  msg := sprintf("image '%v' comes from untrusted registry", [image])
}
```

### Command

```shell
kubectl create cm untrusted-registry --from-file=untrusted-registry.rego
```

