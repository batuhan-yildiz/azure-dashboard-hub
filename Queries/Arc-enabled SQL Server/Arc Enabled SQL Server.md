# How to get total number of Arc-enabled SQL Servers

## Description

Azure Arc enables you to manage servers running outside of Azure using Azure Resource Manager. Each server is represented in Azure as a hybrid compute machine resource.

## Resource Types

### microsoft.hybridcompute/machines

- [Resource format](https://learn.microsoft.com/en-us/azure/templates/microsoft.azurearcdata/sqlserverinstances?pivots=deployment-language-bicep#resource-format)
- [Property values](https://learn.microsoft.com/en-us/azure/templates/microsoft.azurearcdata/sqlserverinstances?pivots=deployment-language-bicep#property-values)

## Code

```
resources
| where  type == "microsoft.azurearcdata/sqlserverinstances"
| summarize ["Arc SQL Servers"] = count()
```