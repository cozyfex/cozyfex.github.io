---
title: "Kubernetes Resource Requirements and Limits"

date: 2021-05-11T19:29:58+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- scheduler
- resource
- resource requirements
- resource limits

keywords:

- kubernetes
- scheduler
- resource
- resource requirements
- resource limits

---

## Default Resource Unit

| CPU | MEM | DISK |  
|:-:|:-:|:-:|  
| 0.5 | 256Mi | |

## Default Resource Limit

| CPU | MEM | DISK |  
|:-:|:-:|:-:|  
| 1 | 512Mi | |

## Lower Limit

| CPU | MEM | DISK |  
|:-:|:-:|:-:|  
| 1m | 256Mi | |

## 1 CPU as in Cloud Services

| Service | Unit |  
|:-:|:-:|  
| AWS | 1 AWS vCPU |  
| Google | 1 GCP Core |  
| Azure | 1 Azure Core / 1 Hyperthread |

## G vs Gi / M vs Mi / K vs Ki

| Unit | Description | Value |  
|:-:|:-:|:-:|  
| 1 G | Gigabyte | 1,000,000,000 bytes |  
| 1 Gi | Gibibyte | 1,073,741,824 bytes |  
| | |  
| 1 M | Megabyte | 1,000,000 bytes |  
| 1 Mi | Mebibyte | 1,048,576 bytes |  
| | |  
| 1 K | Kilobyte | 1,000 bytes |  
| 1 Ki | Kibibyte | 1,024 bytes |

## PODSpec

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: my-app
spec:
  containers:
    - name: nginx-container
      image: nginx
      ports:
        - containerPort: 8080
      resources:
        requests:
          memory: "1Gi"
          cpu: 1
        limits:
          memory: "2Gi"
          cpu: 2

```

## Exceed Limits

| Unit | Action |  
|:-:|:-:|  
| CPU | The `POD` cannot use more CPU over the limit. |  
| MEM | The `POD` can use more memory over the limit, but it's terminated when the usage is over the Physical limit. |

## The resources are not enough in the cluster

The `POD`s are in the Pending status.

## Configure the Default Resource Unit of Container

<sub>configure memory default resource of container</sub>

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
    - default:
        memory: 512Mi
      defaultRequest:
        memory: 256Mi
      type: Container
```

<sub>configure cpu default resource of container</sub>

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-limit-range
spec:
  limits:
    - default:
        cpu: 1
      defaultRequest:
        cpu: 0.5
      type: Container
```

## References

- [Configure Default Memory](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)
- [Configure Default CPU](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/)

