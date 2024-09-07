# How to get total number of Arc-enabled Servers

## Description

Azure Arc enables you to manage servers running outside of Azure using Azure Resource Manager. Each server is represented in Azure as a hybrid compute machine resource.

## Code

```
resources
| where type == "microsoft.hybridcompute/machines"
| summarize ["Arc Servers"] = count()
```