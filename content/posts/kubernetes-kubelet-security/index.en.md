---
title: "Kubernetes Kubelet Security"

date: 2021-07-04T15:26:21+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- kubelet
- security

keywords:

- kubernetes
- kubelet
- security

---

## `kubelet` Port

| Port | Description |  
|:-:|:-|  
| 10250 | Serves API that allows full access |  
| 10255 | Serves API that allows unauthenticated read-only access |

### 10250

```shell
curl -sk https://localhost:10250/pods/
```

```shell
curl -sk https://localhost:10255/metrics
```

### 10255

```shell
curl -sk https://localhost:10255/pods/
```

```shell
curl -sk https://localhost:10255/metrics
```

## `kubelet` Authentication

### Anonymous

#### `kubelet.service`

```shell
ExecStart=/usr/local/bin/kubelet \\
  ...
  --anonymous-auth=false
  ...
```

#### `kubelet-config.yaml`

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
authentication:
  anonymous:
    enabled: false
```

### Certificates

#### `kubelet.service`

```shell
ExecStart=/usr/local/bin/kubelet \\
  ...
  --client-ca-file=/path/to/ca.crt \\
  ...
```

#### `kubelet-config.yaml`

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
authentication:
  x509:
    clientCAFile: /path/to/ca.crt
```

#### Call API

##### Command Way

```shell
curl -sk https://localhost:10250/pods/ -key kubelet-key.pem -cert kubelet-cert.pem
```

##### `kube-apiserver` Configuration

Add below options

```shell
  --kubelet-client-certificate=/path/to/kubelet-cert.pem
  --kubelet-client-key=/path/to/kubelet-key.pem
```

## `kubelet` Authorization

### Default Authorization Mode

#### `kubelet.service`

```shell
ExecStart=/usr/local/bin/kubelet \\
  ...
  --authorization-mode=AlwaysAllow 
  ...
```

#### `kubelet-config.yaml`

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
authentication:
  mode: AlwaysAllow
```

### Webhook Authorization Mode

#### `kubelet.service`

```shell
ExecStart=/usr/local/bin/kubelet \\
  ...
  --authorization-mode=Webhook
  ...
```

#### `kubelet-config.yaml`

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
authentication:
  mode: Webhook
```

## `kubelet` Read Only

### Default

#### `kubelet.service`

```shell
ExecStart=/usr/local/bin/kubelet \\
  ...
  --read-only-port=10255
  ...
```

#### `kubelet-config.yaml`

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
readOnlyPort: 10255
```

### Disable Read Only

#### `kubelet.service`

```shell
ExecStart=/usr/local/bin/kubelet \\
  ...
  --read-only-port=0
  ...
```

#### `kubelet-config.yaml`

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
readOnlyPort: 0
```

## `kubelet` Security Summary

#### `kubelet.service`

```shell
ExecStart=/usr/local/bin/kubelet \\
  ...
  --anonymous-auth=false \\
  --client-ca-file=/path/to/ca.crt \\
  --authorization-mode=Webhook 
  --read-only-port=0
  ...
```

#### `kubelet-config.yaml`

```yaml
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
authentication:
  anonymous:
    enabled: false
  x509:
    clientCAFile: /path/to/ca.crt
authorization:
  mode: Webhook
readOnlyPort: 0
```





