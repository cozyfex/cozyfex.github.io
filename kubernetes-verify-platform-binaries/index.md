# Kubernetes Verify Platform Binaries


## Verify

{{<admonition note "Download" true>}}  
Download from [Releases](https://github.com/kubernetes/kubernetes/releases)
{{</admonition>}}

```shell
wget -O kubernetes.tar.gz https://github.com/kubernetes/kubernetes/archive/refs/tags/v1.21.2.tar.gz

curl https://github.com/kubernetes/kubernetes/archive/refs/tags/v1.21.2.tar.gz -L -o kubernetes.tar.gz
```

## Generate SHA

### MacOS

```shell
shasum -a 512 kubernetes.tar.gz
```

### Linux

```shell
sha512sum kubernetes.tar.gz
```

## Compare with Released SHA512

{{<admonition note "Download" true>}}  
In the [Releases Notes](https://kubernetes.io/releases/notes/), you can compare with SHA512.  
{{</admonition>}}
