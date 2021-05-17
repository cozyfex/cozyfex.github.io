# Kubernetes Environment Variables


## Docker

```shell
docker run -e APP_COLOR=blue simple-webapp-color

docker run -e APP_COLOR=green simple-webapp-color

docker run -e APP_COLOR=pink simple-webapp-color
```

## Kubernetes

```yaml
apiVersion: v1
kind:
  Pod
metadata:
  name: simple-webapp-color
spec:
  containers:
    - name: simple-webapp-color
      image: simple-webapp-color
      ports:
        - containerPort: 8080
      env:
        - name: APP_COLOR
          value: pink
```

