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

## OPA in Kubernetes

{{<admonition note "OPA in Kubernetes" true>}}  
The OPA in Kubernetes is using `ValidatingAdmissionWebhook` of admission controllers.  
{{</admonition>}}

## Create a ConfigMap for OPA

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

