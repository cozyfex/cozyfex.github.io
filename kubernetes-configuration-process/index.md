# Kubernetes Configuration Process


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

