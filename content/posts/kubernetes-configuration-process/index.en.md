---
title: "Kubernetes Configuration Process"

date: 2021-05-09T17:31:07+09:00

author: CozyFex

categories:

- kubernetes
- k8s
- orchestration

tags:

- kubernetes
- configuration
- local
- live
- last applied

keywords:

- kubernetes
- configuration
- local
- live
- last applied

---

## Process

{{<mermaid>}}  
stateDiagram  
Local --> Live Live --> LastApplied  
{{</mermaid>}}

## Format of Configuration

| Type         | Format |
|--------------|--------|
| Local        | YAML   |
| Live         | YAML   |
| Last Applied | JSON   |
