---
title: "Docker Minimize Base Image"

date: 2021-07-08T21:12:46+09:00

author: CozyFex

categories:

- docker

tags:

- docker
- base image
- parent image

keywords:

- docker
- base image
- parent image

---

## Base and Parent Image

| Type | Description |  
|:-:|:-|  
| Base Image | A base image has FROM scratch in its Dockerfile. |  
| Parent Image | A parent image is the image that your image is based on. It refers to the contents of the FROM directive in the Dockerfile. |

## Base Image Choose Rules

### Authenticity

{{<admonition note Authenticity true>}}  
Check `OFFICIAL IMAGE` on docker hub.  
{{</admonition>}}

### Up-to-date

{{<admonition note Up-to-date true>}}  
Check update time on docker hub.  
{{</admonition>}}

### Slim and Minimal Image

* Only install necessary packages
    * Remove Shells, Package Managers, Tools
* Maintain images for each environment
    * Development - Including debug tools
    * Production - Lean
* Use multi-stage builds to create lean production ready images

### Distroless Docker Images

* Contains
    * Application
    * Runtime Dependencies
* Not Contains
    * Package Managers
    * Shells
    * Network Tools
    * Text Editors
    * etc

### Vulnerability Scanning

```shell
trivy image httpd
```

```shell
trivy image httpd:alpine
```