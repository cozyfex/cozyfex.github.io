---
title: "Kubernetes Node Affinity"

date: 2021-05-11T16:11:34+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- scheduler
- affinity

keywords:

- kubernetes
- scheduler
- affinity

---

## 왜 Node Affinity 가 필요한가?

{{<admonition note "Node Affinity" true>}}  
`nodeSelector` 는 POD 들을 특정한 Node 에 스케줄링 하기에는 충분한 옵션이 아니다.  
`nodeSelector` 보다 더 복잡한 제한들을 만들고 싶다면 `affinity` 를 사용해야 한다.  
{{</admonition>}}

## `nodeSelector` 와 `affinity` 비교

<sub>`nodeSelctor` node-selector.yml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx
      image: nginx
  nodeSelector:
    size: Large
```

<sub>`affinity` affinity.yml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx
      image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: size
                operator: In
                values:
                  - Large
```

## `affinity` 타입

{{<admonition note "DuringScheduling" true>}}  
쿠버네티스에서 `schedule` 의 의미는 `POD` 을 Node 에 바인딩 하는것을 의미한다.  
그래서 `DuringScheduling` 의 의미는 "바인딩을 할 때, 어떻게 적용 할 것인가" 란 뜻이다.  
{{</admonition>}}

{{<admonition note "DuringExecution" true>}}
`DuringExecution` 의 의미는 "실행중인 `POD` 들에 어떻게 적용 할 것인가" 이다.  
{{</admonition>}}

| Type | Action |  
|:-:|:-:|  
| `required` | 설정을 적용한다. |  
| `preferred` | 적용 하려고 시도는 하지만, 필수는 아니다. |  
| `ignore` | 아무것도 적용하지 않는다. |

## `affinity` 에서 `operator` 타입

| Type | Action |  
|:-:|:-:|  
| In | SQL 에서 'IN' 과 같다. `values` 는 배열이다. |  
| NotIn | SQL 에서 `NOT IN` 과 같다. `values` 는 배열이다. |  
| Exists | `key` 의 존재만 체크한다. `values` 는 설정하지 않는다. |

## `affinity` 예제

{{<admonition note "시나리오" true>}}  
여기 3개의 노드가 있다.  
각 노드는 `key`가 'size' 라는 레이블을 가지고 있다.  
레이블은 각각 `Large`, `Medium`, `Small` 이다.

```shell
# Large on node01
kubectl label nodes node01 size=Large

# Medium on node02
kubectl label nodes node02 size=Medium

# Small on node03
kubectl label nodes node03 size=Small

# Check labels
kubectl get nodes --show-labels
```

{{</admonition>}}

### Large Node 에 POD 들을 스케줄링

#### `nodeSelector` 사용

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
  nodeSelector: Large
```

#### `affinity` 사용

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: size
                operator: In
                values:
                  - Large
```

### Small Node 에 POD 들을 스케줄링 하지 않기

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: size
                operator: NotIn
                values:
                  - Small
```

### Medium 또는 Small Node 에 POD 들을 스케줄링

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: nginx-container
      image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: size
                operator: In
                values:
                  - Medinum
                  - Small
```

## Deployment 와 `affinity`

<sub>blue-deployment.yml</sub>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blue
spec:
  template:
    metadata:
      name: blue-pod
      labels:
        app: blue-app
    spec:
      containers:
        - name: nginx-container
          image: nginx
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: color
                    operator: In
                    values:
                      - blue
  replicas: 3
  selector:
    matchLabels:
      app: blue-app
```

<sub>red-deployment.yml</sub>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: red
spec:
  replicas: 2
  selector:
    matchLabels:
      app: red-app
  template:
    metadata:
      name: red-pod
      labels:
        app: red-app
    spec:
      containers:
        - name: nginx-container
          image: nginx
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: node-role.kubernetes.io/master
                    operator: Exists
```
