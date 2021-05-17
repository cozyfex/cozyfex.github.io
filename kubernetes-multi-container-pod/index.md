# Kubernetes Multi-Container POD


## Multi-Container POD

{{<admonition note "Multi-Container Lifecycle">}}  
The containers of the `POD` have same lifecycle.  
{{</admonition>}}

<sub>pod-definition.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp
  labels:
    name: simple-webapp
spec:
  containers:
    - name: simple-webapp
      image: simple-webapp
      ports:
        - containerPort: 8080
    - name: log-agent
      image: log-agent
```
