---
title: "Kubernetes KubeConfig"

date: 2021-05-23T18:50:23+09:00

author: CozyFex

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

## KubeConfig Structure

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
Information of Clusters.

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
Information of Users.

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
Information of Contexts.  
This is combines between Clusters and Users.  
In additional, you can set specific `namespace` of the target cluster.

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
You can set current context.  
Of course the value is from one of the name of `contexts`.

```yaml
current-context: user1@cluster1
```

{{</admonition>}}

## Show PODs

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

### Command

#### Options

```shell
kubectl get pods \
  --server https://cluster1:6443
  --client-key admin.key
  --client-certificate admin.crt
  --certificate-authority ca.crt
```

#### With KubeConfig File

```shell
# The file is '~/.kube/config`
kubectl get pods --kubeconfig config
```

## Config in Kubernetes

### View Config

```shell
kubectl config view
```

### View Specific KubeConfig

```shell
kubectl config view --kubeconfig=my-custom-config
```

### View Specific Context

```shell
kubectl config use-context user1@cluster2
```

### Command Help

```shell
kubectl config -h
```

| Command | Description |  
|:-:|:-|  
| current-context | Displays the current-context |  
| delete-cluster | Delete the specified cluster from the kubeconfig |  
| delete-context | Delete the specified context from the kubeconfig |  
| get-clusters | Display clusters defined in the kubeconfig |  
| get-contexts | Describe one or many contexts |  
| rename-context | Renames a context from the kubeconfig file. |  
| set | Sets an individual value in a kubeconfig file |  
| set-cluster | Sets a cluster entry in kubeconfig |  
| set-context | Sets a context entry in kubeconfig |  
| set-credentials | Sets a user entry in kubeconfig |  
| unset | Unsets an individual value in a kubeconfig file |  
| use-context | Sets the current-context in a kubeconfig file |  
| view | Display merged kubeconfig settings or a specified kubeconfig file |

## KueConfig YAML

{{<admonition note "CRT/KEY to Encoded Data in YAML">}}  
If you want to import `*.crt` or `*.key` files to a YAML file, follow below directions.

```shell
cat ca1.crt | base64
```

You have to replace Path Attribute to Data Attribute in the YAML.  
Copy the result string data to a value of Data Attribute.

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