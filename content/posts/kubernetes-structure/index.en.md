---
title: "Kubernetes Structure"

date: 2021-04-25T16:44:32+09:00

author: CozyFex

categories:

- kubernetes

tags:

- kubernetes
- orchestration
- container

keywords:

- kubernetes
- orchestration
- container

---

## Kubernetes Structure

{{<admonition note>}}
Here's explanation of Kubernetes structure that I understand.\
So, it could be wrong.\
I just record for memory.
{{</admonition>}}

## Cluster

A computing set they have a same purpose.\
In Kubernetes, there are `Master` and `Node`.

## Master

A controller for managing the `Cluster`.

## Node

This is a physical machine that is for deploying containers.

## POD

The `POD` is a deployment unit in Kubernetes.\
A `POD` has multiple containers.\
The Kubernetes is managing this unit.\
In a `Node` can have multiple `POD`.

{{<admonition note "Reason why Kubernetes manage by unit POD" true>}}  
In a `POD` is sharing IP and Port.  
In a `POD` can share disk volumes.  
{{</admonition>}}

## Container

This is a technology for virtualization based on linux.

