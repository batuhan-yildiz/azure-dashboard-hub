# How to get Arc-enabled Server insights

## Description

Azure Arc enables you to manage servers running outside of Azure using Azure Resource Manager. Each server is represented in Azure as a hybrid compute machine resource.

## Resource Types

### microsoft.hybridcompute/machines

- [Resource format](https://learn.microsoft.com/en-us/azure/templates/microsoft.hybridcompute/machines?pivots=deployment-language-bicep#resource-format)
- [Property values](https://learn.microsoft.com/en-us/azure/templates/microsoft.hybridcompute/machines?pivots=deployment-language-bicep#property-values)

## Sample Queries

### Total number of Arc-enabled servers

```
resources
| where type == "microsoft.hybridcompute/machines"
| summarize ["Arc Servers"] = count()
```

### Arc-enabled servers Details

- [kind](https://learn.microsoft.com/en-us/dotnet/api/microsoft.azure.powershell.cmdlets.connectedmachine.models.imachine.kind?view=az-ps-latest): Indicates which kind of Arc machine placement on-premises, such as HCI, SCVMM or VMware etc (AVS, HCI, SCVMM, VMware, EPS, GCP, AWS).
- [serverCloudProvider](https://learn.microsoft.com/en-us/dotnet/api/microsoft.azure.powershell.cmdlets.connectedmachine.models.imachine.cloudmetadataprovider?view=az-ps-latest): Specifies the cloud provider (Azure/AWS/GCP...).
- [osName](https://learn.microsoft.com/en-us/dotnet/api/microsoft.azure.powershell.cmdlets.connectedmachine.models.imachine.osname?view=az-ps-latest): The Operating System running on the hybrid machine.
- [osType](https://learn.microsoft.com/en-us/dotnet/api/microsoft.azure.powershell.cmdlets.connectedmachine.models.imachine.ostype?view=az-ps-latest): The type of Operating System (windows/linux).
- [osSku](https://learn.microsoft.com/en-us/dotnet/api/microsoft.azure.powershell.cmdlets.connectedmachine.models.imachine.ossku?view=az-ps-latest): Specifies the Operating System product SKU.
- [licenseAssignmentState](https://learn.microsoft.com/en-us/dotnet/api/microsoft.azure.powershell.cmdlets.connectedmachine.models.imachineproperties.esuprofilelicenseassignmentstate?view=az-ps-latest): Describes the license assignment state (Assigned or NotAssigned).
- [esuEligibility](https://learn.microsoft.com/en-us/dotnet/api/microsoft.azure.powershell.cmdlets.connectedmachine.models.imachineproperties.esuprofileesueligibility?view=az-ps-latest): Indicates the eligibility state of ESU (Eligible, Ineligible, Unknown).
- [esuKeyState](https://learn.microsoft.com/en-us/dotnet/api/microsoft.azure.powershell.cmdlets.connectedmachine.models.imachineproperties.esuprofileesukeystate?view=az-ps-latest): Indicates whether there is an ESU Key currently active for the machine (Inactive, Active).

```
resources    
| where type == "microsoft.hybridcompute/machines"
| extend serverId = tolower(tostring(id))
| extend serverName = name
| extend serverKind = kind
| extend serverSubscriptionId = subscriptionId
| extend serverLocation = location
| extend serverResourceGroup = resourceGroup
| extend serverTags = tags
| extend serverProvisioningState = tostring(properties.provisioningState)
| extend serverStatus = tostring(properties.status)
| extend serverOsType = tostring(properties.osType)
| extend serverDisplayName = tostring(properties.displayName)
| extend serverTotalPhysicalMemoryInGigabytes = toreal(properties.detectedProperties.totalPhysicalMemoryInGigabytes)
| extend serverProcessorCount = properties.detectedProperties.processorCount
| extend serverCoreCount = properties.detectedProperties.coreCount
| extend serverLogicalCoreCount = properties.detectedProperties.logicalCoreCount
| extend serverMssqldiscovered = properties.detectedProperties.mssqldiscovered
| extend serverCloudProvider = properties.detectedProperties.cloudprovider
| extend serverComputerName = tostring(properties.osProfile.computerName)
| extend serverEsuLicenseAssignment = tostring(properties.licenseProfile.esuProfile.licenseAssignmentState)
| extend esuCount = iff(serverEsuLicenseAssignment == "NotAssigned",0,1)
| extend serverEsuEligibility = tostring(properties.licenseProfile.esuProfile.esuEligibility)
| extend serverEsuKeyState = tostring(properties.licenseProfile.esuProfile.esuKeyState)
| extend serverOsName = tostring(properties.osName)
| extend serverOsSKU = tostring(properties.osSku)
| extend serverAgentVersion = tostring(properties.agentVersion)
| project ["Server ID"] = serverId, ["Server Kind"] = serverKind, 
    ["Server Name"] = serverName, ["OS Name"] = serverOsName,
    ["OS Type"] = serverOsType, ["OS SKU"] = serverOsSKU,
    ["Provisioning State"] = serverProvisioningState, ["Server Status"] = serverStatus,
    ["MSSQL Discovered"] = serverMssqldiscovered, ["Total Memory (GB)"] = serverTotalPhysicalMemoryInGigabytes,
    ["Processor"] = serverProcessorCount, ["Server Cores"] = serverCoreCount, ["Server Logical Cores"] = serverLogicalCoreCount,
    ["ESU Eligibility"] = serverEsuEligibility, ["ESU License"] = serverEsuLicenseAssignment,
    ["ESU Key State"] = serverEsuKeyState, ["ESU Count"] = esuCount,
    ["Cloud Provider"] = serverCloudProvider, ["Agent Version"] = serverAgentVersion,
    ["Display Name"] = serverDisplayName, ["Computer Name"] = serverComputerName,
    ["Subscription Id"] = serverSubscriptionId, ["Location"] = serverLocation, ["Resource Group"] = serverResourceGroup,
    ["Server Tags"] = serverTags
```
