# Kubernetes 설정 프로세스


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

