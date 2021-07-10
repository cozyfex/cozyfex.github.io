# Kubernetes OPA


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


