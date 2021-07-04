# Kubernetes Kubectl Proxy


## `kubectl` Certificate Config

```shell
cat ~/.kube/config
```

## API Calling Ways

### `kubectl`

```shell
kubectl get nodes
```

### Webhook

```shell
curl http://<kube-api-server-ip>:6443 -k
```

```shell
curl http://<kube-api-server-ip>:6443 –k \\
      --key admin.key \\
      --cert admin.crt \\
      --cacert ca.crt
```

## `kubectl` Proxy

### Start Proxy

```shell
kubectl proxy
```

```
Starting to serve on 127.0.0.1:8001
```

### Call API

```shell
curl http://localhost:8001 -k
```

```shell
curl http://localhost:8001/api/v1/namespaces/default/services/nginx/proxy/
```

## `kubectl` Port Forward

```shell
kubectl port-forward service/nginx 28080:80
```

```shell
curl http://localhost:28080/
```

## Summary

{{<image src="kubectl-proxy.png" width="100%">}}




