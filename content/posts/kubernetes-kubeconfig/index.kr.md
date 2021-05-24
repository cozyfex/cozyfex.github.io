---
title: "Kubernetes KubeConfig"

date: 2021-05-23T18:50:23+09:00

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- security
- tls
- certificate
- kubeconfig

keywords:

- kubernetes
- security
- tls
- certificate
- kubeconfig

---

## KubeConfig 구조

{{<mermaid>}}  
graph TD;  
CL(Clusters) --> CN(Contexts)  
U(Users) --> CN  
{{</mermaid>}}

### KubeConfig File

```shell
cd ~/.kube/config
```

```shell
# Cluster Server
--server my-kube-playground:6443
# User Key
--client-key admin.key
# User Certificate
--client-certificate admin.crt
# Cluster Certificate Authority
--certificate-authority ca.crt
```

### KubeConfig YAML

#### Clusters

{{<admonition note Clusters true>}}  
클러스터들의 정보

```yaml
clusters:
  - name: cluster1
    cluster:
      certificate-authority: ca1.crt
      server: https://cluster1:6443
  - name: cluster2
    cluster:
      certificate-authority: ca2.crt
      server: https://cluster2:6443
```

{{</admonition>}}

#### Users

{{<admonition note Users true>}}  
사용자들의 정보

```yaml
users:
  - name: user1
    user:
      client-certificate: user1.crt
      client-key: user1.key
  - name: user2
    user:
      client-certificate: user2.crt
      client-key: user2.key
```

{{</admonition>}}

#### Contexts

{{<admonition note Contexts true>}}  
컨텍스트들의 정보  
컨텍스트는 클러스터 정보와 사용자 정보의 결합이다.  
여기에 특정한 `namespace` 를 설정할 수 있다.

```yaml
contexts:
  - name: user1@cluster1
    context:
      cluster: cluster1
      user: user1
      namespace: dev
  - name: user2@cluster1
    context:
      cluster: cluster1
      user: user2
      namespace: prod
  - name: user2@cluster2
    context:
      cluster: cluster2
      user: user2
```

{{</admonition>}}

#### Current Context

{{<admonition note "Current Context" true>}}  
최근의 컨텍스트를 설정 할 수 있다.  
물론 이 값은 `contexts` 의 이름에서 온 것이다.

```yaml
current-context: user1@cluster1
```

{{</admonition>}}

## POD 확인

### API

```shell
curl https://cluster1:6443/api/v1/pods \
  --key admin.key
  --cert admin.crt
  --cacert ca.crt
```

```json
{
  "kind": "PodList",
  "apiVersion": "v1",
  "metadata": {
    "selfLink": "/api/v1/pods"
  },
  "items": []
}
```

### 명령

#### Options

```shell
kubectl get pods \
  --server https://cluster1:6443
  --client-key admin.key
  --client-certificate admin.crt
  --certificate-authority ca.crt
```

#### KubeConfig 파일로 설정

```shell
# The file is '~/.kube/config`
kubectl get pods --kubeconfig config
```

## 쿠버네티스의 Config

### Config 확인

```shell
kubectl config view
```

### 특정한 KubeConfig 확인

```shell
kubectl config view --kubeconfig=my-custom-config
```

### 특정한 Context 확인

```shell
kubectl config use-context user1@cluster2
```

### 명령 도움말

```shell
kubectl config -h
```

| 명령 | 설명 |  
|:-:|:-|  
| current-context | current-context 보기 |  
| delete-cluster | kubeconfig 에서 특정 클러스터 삭제 |  
| delete-context | kubeconfig 에서 특정 컨텍스트 삭제 |  
| get-clusters | kubeconfig 에 정의된 클러스터들 보기 |  
| get-contexts | 하나 또는 여러개의 컨텍스트 상세설명 |  
| rename-context | kubeconfig 파일에서 컨텍스트 이름 변경 |  
| set | kubeconfig 파일에서 개별 값을 설정 |  
| set-cluster | kubeconfig 에 클러스터 항목을 설정 |  
| set-context | kubeconfig 에 컨텍스트 항목을 설정 |  
| set-credentials | kubeconfig 에 사용자 항목을 설정 |  
| unset | kubeconfig 파일에서 개별 값을 설정 해제 |  
| use-context | kubeconfig 파일에 current-context 설정 |  
| view | 병합된 kubeconfig 세팅 또는 특정한 kubeconfig 파일 보기 |

## KueConfig YAML

{{<admonition note "CRT/KEY to Encoded Data in YAML">}}  
`*.crt` 또는 `*.key` 파일을 YAML 에 포함시키려면, 아래의 지시들을 따르면 된다.

```shell
cat ca1.crt | base64
```

YAML 파일에서 Path Attribute 를 Data Attribute 로 변경해야 한다.  
그리고 위의 결과 텍스트 데이터를 Data Attribute 값으로 복사한다.

| Path Attribute | Data Attribute |  
|:-:|:-:|  
| certificate-authority | certificate-authority-data |  
| client-certificate | client-certificate-data |  
| client-key | client-key-data |

{{</admonition>}}

```yaml
apiVersion: v1
kind: Config

current-context: user1@cluster1

clusters:
  - name: cluster1
    cluster:
      certificate-authority: ca1.crt
      server: https://cluster1:6443
  - name: cluster2
    cluster:
      certificate-authority: ca2.crt
      server: https://cluster2:6443

users:
  - name: user1
    user:
      client-certificate: user1.crt
      client-key: user1.key
  - name: user2
    user:
      client-certificate: user2.crt
      client-key: user2.key

contexts:
  - name: user1@cluster1
    context:
      cluster: cluster1
      user: user1
      namespace: dev
  - name: user2@cluster1
    context:
      cluster: cluster1
      user: user2
      namespace: prod
  - name: user2@cluster2
    context:
      cluster: cluster2
      user: user2
```