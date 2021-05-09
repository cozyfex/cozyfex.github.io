---
title: "Kubernetes 설정 프로세스"

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

## 프로세스

{{<mermaid>}}  
stateDiagram  
Local --> Live Live --> LastApplied  
{{</mermaid>}}

## 설정 포멧

| Type         | Format |
|--------------|--------|
| Local        | YAML   |
| Live         | YAML   |
| Last Applied | JSON   |
