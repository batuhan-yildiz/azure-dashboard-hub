# How to get Arc registered ESU insights

## Description

Azure Arc enables you to manage servers running outside of Azure using Azure Resource Manager. Each server is represented in Azure as a hybrid compute machine resource.

## Resource Types

### microsoft.hybridcompute/machines

- [Resource format](https://learn.microsoft.com/en-us/azure/templates/microsoft.hybridcompute/machines?pivots=deployment-language-bicep#resource-format)
- [Property values](https://learn.microsoft.com/en-us/azure/templates/microsoft.hybridcompute/machines?pivots=deployment-language-bicep#property-values)

## Sample Queries

### Total number of Arc-enabled servers by ESU status and filtered for Windows Server 2012

- [licenseAssignmentState](https://learn.microsoft.com/en-us/dotnet/api/microsoft.azure.powershell.cmdlets.connectedmachine.models.imachineproperties.esuprofilelicenseassignmentstate?view=az-ps-latest): Describes the license assignment state (Assigned or NotAssigned).
- [esuEligibility](https://learn.microsoft.com/en-us/dotnet/api/microsoft.azure.powershell.cmdlets.connectedmachine.models.imachineproperties.esuprofileesueligibility?view=az-ps-latest): Indicates the eligibility state of ESU (Eligible, Ineligible, Unknown).
- [esuKeyState](https://learn.microsoft.com/en-us/dotnet/api/microsoft.azure.powershell.cmdlets.connectedmachine.models.imachineproperties.esuprofileesukeystate?view=az-ps-latest): Indicates whether there is an ESU Key currently active for the machine (Inactive, Active).

```
resources
| where type == 'microsoft.hybridcompute/machines'
| extend serverOsSKU = tostring(properties.osSku)
| extend serverEsuLicenseAssignment = tostring(properties.licenseProfile.esuProfile.licenseAssignmentState)
| extend esuCount = iff(serverEsuLicenseAssignment == "NotAssigned",0,1)
| extend serverEsuEligibility = tostring(properties.licenseProfile.esuProfile.esuEligibility)
| extend serverEsuKeyState = tostring(properties.licenseProfile.esuProfile.esuKeyState)
| where serverOsSKU contains "Windows Server 2012" 
| summarize ["Arc Servers"] = count() by 
    ["ESU License Assignment"] = serverEsuLicenseAssignment, ["ESU Key State"] = serverEsuKeyState
```

