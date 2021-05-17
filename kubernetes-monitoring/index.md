# Kubernetes Monitoring


## Monitoring Services

- [METRICS SERVER](https://github.com/kubernetes-sigs/metrics-server)
- [Prometheus](https://prometheus.io/)
- [Elastic Stack](https://www.elastic.co/)
- [DATADOG](https://www.datadoghq.com/)
- [dynatrace](https://www.dynatrace.com/)

{{<admonition note HEAPSTER true>}}  
`HEAPSTER` is a monitoring service in Kubernetes, but it's deprecated now.  
{{</admonition>}}

## METRICS SERVER

`METRICS SERVER` is IN-MEMORY Service.  
So you cannot see history of the cluster.

### Install METRICS SERVER

#### minikube

```shell
minikube addons enable metrics-server
```

#### Others

Check Installation Section in [METRICS SERVER](https://github.com/kubernetes-sigs/metrics-server)

```shell
# Install all YAML files in current directory.
kubectl create -f .
```

### METRICS SERVER Commands

#### Top Nodes

```shell
watch kubectl top nodes
watch "kubectl top nodes"
```

#### Top PODs

```shell
watch kubectl top pods
watch "kubectl top pods"
```

