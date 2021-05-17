# Kubernetes 다중 컨체이너 POD


## 다중 컨테이너 POD

{{<admonition note "다중 컨테이너 생명 주기">}}  
`POD` 의 컨테이너들은 같은 생명 주기를 같는다.  
{{</admonition>}}

<sub>pod-definition.yaml</sub>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp
  labels:
    name: simple-webapp
spec:
  containers:
    - name: simple-webapp
      image: simple-webapp
      ports:
        - containerPort: 8080
    - name: log-agent
      image: log-agent
```
