---
title: "Kubernetes 자원 요구사항과 제한"

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

## 기본 자원 단위

| CPU | MEM | DISK |  
|:-:|:-:|:-:|  
| 0.5 | 256Mi | |

## 기본 자원 제한

| CPU | MEM | DISK |  
|:-:|:-:|:-:|  
| 1 | 512Mi | |

## 하한 제한

| CPU | MEM | DISK |  
|:-:|:-:|:-:|  
| 1m | 256Mi | |

## 클라우드 서비스 별 1 CPU

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

## 제한 초과

| Unit | Action |  
|:-:|:-:|  
| CPU | `POD` 은 제한된 CPU 이상을 사용하지 못한다. |  
| MEM | `POD` 은 제한 이상의 메모리를 사용 할 수 있지만, 물리적인 제한을 넘었을 경우 종료된다. |

## 클러스터에 자원이 충분하지 않은 경우

`POD` 은 Pending 상태가 된다.

## 컨테이너의 기본 자원 단위 설정

<sub>컨테이너의 메모리 기본 자원 단위 설정</sub>

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

<sub>컨테이너의 CPU 기본 자원 단위 설정</sub>

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

- [기본 메모리 설정](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/)
- [기본 CPU 설정](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/)

