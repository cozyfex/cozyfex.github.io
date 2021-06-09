---
title: "Kubernetes JSONPath"

date: 2021-06-06T01:45:37+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration
- jsonpath

tags:

- kubernetes
- jsonpath

keywords:

- kubernetes
- jsonpath

---

## JSONPath 란?

{{<admonition note JSONPath true>}}  
JSONPath 는 JSON 에서 데이터를 셀렉팅 하기 위한것이다.  
[JSONPath Online Evaluator](https://jsonpath.com/) 에서 테스트 할 수 있다.  
{{</admonition>}}

## JSONPath Expressions

| JSONPath | Description |  
|:-|:-|  
| `$` | The root object/element |  
| `@` | The current object/element |  
| `.` or `[]` | Child operator |  
| `..` | Recursive descent. |  
| `*` | Wildcard. All objects/elements regardless their names. |  
| `[]` | Subscript operator. XPath uses it to iterate over element collections and for predicates. In Javascript and JSON it is the native array operator. |  
| `[,]` | Union operator in XPath results in a combination of node sets. JSONPath allows alternate names or array indices as a set. |  
| `[start:end:step]` | Array slice operator |  
| `?()` | Applies a filter (script) expression. |  
| `()` | Script expression, using the underlying script engine. |

## Examples

```shell
kubectl get nodes -o json > /opt/outputs/nodes.json
```

```shell
kubectl get nodes node01 -o json > /opt/outputs/node01.json
```

```shell
kubectl get nodes -o=jsonpath='{$.items[*].metadata.name}'
```

```shell
kubectl get nodes -o=jsonpath='{$.items[*].status.nodeInfo.osImage}'
```

```shell
kubectl config view --kubeconfig=/root/my-kube-config -o=jsonpath='{$.users[*].name}'
```

```shell
kubectl get pv --sort-by='{.spec.capacity.storage}'
```

```shell
kubectl get pv --sort-by=.spec.capacity.storage -o=custom-columns=NAME:.metadata.name,CAPACITY:.spec.capacity.storage
```

```shell
kubectl config view --kubeconfig=my-kube-config -o jsonpath="{.contexts[?(@.context.user=='aws-user')].name}"
```