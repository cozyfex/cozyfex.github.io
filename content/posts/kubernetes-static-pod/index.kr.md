---
title: "Kubernetes Static Pod"

date: 2021-05-15T16:43:11+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- static pod

keywords:

- kubernetes
- static pod

---

## Static PODs vs DaemonSets

| Static PODs | DaemonSets |  
|:-:|:-:|  
| kubelet 생성 | kube-api server 생성 (DaemonSet Controller) |  
| Static PODs 같은 제어판 컴포넌트 배포 | Monitoring Agents, Logging Agents 등을 노드들에 배포 |  
| kube-scheduler 무시 | kube-scheduler 무시 |

## Default Static PODs Location

{{<admonition note "Static PODs" true>}}  
`/etc/kubernetes/manifests` 내의 YAML 파일이 생성 또는 삭제 될 때, `POD` 들을 자동으로 생성, 삭제 한다.  
{{</admonition>}}

```shell
cd /etc/kubernetes/manifests
```

## 노드에 몇개의 `POD` 들이 존재 하는지 확인

```shell
ls /etc/kubernetes/manifests
```

## 명령적 이용

```shell
kubectl run --restart=Never --image=nginx --dry-run=client -o yaml --command sleep 1000 > /etc/kubernetes/manifests/nginx.yaml
```

## Static PODs 설정 확인

### 대상 `POD` 의 노드 확인

{{<admonition note "어떻게 Static POD 을 구별 할 수 있나?" true>}}  
`POD` 의 이름에는 `-master` 같은 `-<node-name>` 형식의 접미사가 붙는다.  
{{</admonition>}}

```shell
kubectl get pods -o wide

kubectl get pods --all-namespaces -o wite | grep "\-master"
```

### 해당 노드로 접속

```shell
ssh <node-name>
```

### 설정상의 경로 확인

```shell
# Check '--config' option
ps -ef | grep kubelet

```

### `staticPodPath` 옵션 확인

```shell
# Check 'staticPodPath'
grep -i staticpod /var/lib/kubelet/config.yaml
```
