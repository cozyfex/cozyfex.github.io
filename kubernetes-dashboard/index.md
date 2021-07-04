# Kubernetes Dashboard


## Kubernetes Web UI

{{<admonition note "Web UI" true>}}  
By default, dashboard can access in the Cluster including your laptop.  
[Kubernetes Web UI](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)  
{{</admonition>}}

## Deploying

```shell
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml
```

## Run Proxy

```shell
kubectl proxy
```

## Access Dashboard

[http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/)

## Dashboard Authentication

{{<admonition note "Web UI" true>}}  
You can find the way to authenticate with dashbaord.  
{{</admonition>}}


