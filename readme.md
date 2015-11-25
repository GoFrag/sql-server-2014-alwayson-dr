# Extend existing SQL Server 2014 AlwaysOn AG for Disaster Recovery

This template deploys an additional SQL Server 2014 AlwaysOn AG cluster node to an existing SQL Server AlwaysOn AG cluster in a remote Azure datacenter region for Disaster Recovery.  This additonal node can be leveraged for hosting Async Replicas of databases in an existing AlwaysOn Availability Group.

This template creates the following resources:

+	One Storage Account
+	One internal load balancer
+	One VM running SQL Server 2014 in an existing SQL Server 2014 AlwaysOn cluster

A SQL Server always on listener is created using the internal load balancer.

# Known Issues

This template is serial in nature for deploying some of the resources, due to some issues between the platform agent and the DSC extension which cause problems when multiple VM and\or extension resources are deployed concurrently. 

## Notes

+	The default settings for storage are to deploy using **premium storage**.  The SQL VM uses two P30 disks each (for data and log).  These sizes can be changed by changing the relevant variables. In addition there is a P10 Disk used for the VM OS Disk.

+ 	The default settings for compute require that you have at least 4 cores of free quota to deploy.

+ 	The image used to create this deployment are
	+ 	SQL Server - Latest SQL Server 2014 Enterprise on Windows Server 2012 R2 Image

+ 	The image configuration is defined in variables - details below - but the scripts that configure this deployment have only been tested with these versions and may not work on other images.

+	To successfully deploy this template, be sure that the subnet to which the SQL VMs are being deployed already exists on the specified Azure virtual network, AND this subnet should be defined in Active Directory Sites and Services for the appropriate AD site in which the closest domain controllers are configured.


Click the button below to deploy from the portal

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Frobotechredmond%2Fsql-server-2014-alwayson-dr%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## Deploying from PowerShell

For details on how to install and configure Azure Powershell see [here].(https://azure.microsoft.com/en-us/documentation/articles/powershell-install-configure/)

Launch a PowerShell console

Ensure that you are in Resource Manager Mode

```PowerShell

Switch-AzureMode AzureResourceManager

```
Change working folder to the folder containing this template

```PowerShell

New-AzureResourceGroup -Name "<new resourcegroup name>" -Location "<new resourcegroup location>"  -TemplateParameterFile .\azuredeploy-parameters.json -TemplateFile .\azuredeploy.json

```

## Template Parameters

When deploying from this template, you will be prompted for the parameters listed below.

|Name|Description|
|:---|:---------------------|
|newStorageAccountNamePrefix|Naming prefix for each new storage account created. Three storage accounts will be created using this string as a prefix for the name. 18-char max, lowercase alpha|
|storageAccountType|Type of new Storage Accounts (Standard_LRS, Standard_GRS, Standard_RAGRS or Premium_LRS) to be created to store VM disks|
|vmNamePrefix|Naming prefix for each VM name. 8-char max, lowercase alpha|
|sqlVMSize|Size of the SQL VM instances to be created|
|sqlWitnessVMSize|Size of the Witness VM instance to be created|
|sqlServerServiceAccountUserName|Service account name for SQL Server services|
|sqlServerServiceAccountPassword|Service account password for SQL Server services|
|virtualNetworkId|Resource ID of the existing VNET. You can find the Resource ID for the VNET on the Properties blade of the VNET.|
|sqlSubnetName|Name of the existing subnet in the existing VNET to which the SQL & Witness VMs should be deployed|
|domainName|DNS domain name for existing Active Directory domain|
|adPDCVMName|Computer name of the existing Primary AD domain controller & DNS server|
|primaryAdIpAddress|IP address of the existing Primary AD domain controller & DNS server|
|secondaryAdIpAddress|IP address of the existing Secondary AD domain controller & DNS server|
|sqlLBIPAddress|IP address of ILB for the new SQL Server AlwaysOn listener to be created|
|adminUsername|Name of an existing Admin user account for the Active Directory domain|
|adminPassword|Password for the existing Admin user account for the Active Directory domain|
|location|Region in which to deploy the new resources|
|dataBaseNames|An array of database names. Each database will be created and added to the availability group|
|assetLocation|Location of resources upon which this template is dependent, such as nested templates and DSC modules|

## Notable Variables

|Name|Description|
|:---|:---------------------|
|sqlLBName|Resource name of the SQL ILB|
|sqlAvailabilitySetName|Name for Azure availability set for SQL and Witness VMs|
|lbFE|Load balancer front-end pool name|
|lbBE|Load balancer back-endpool name|
|sqlWitnessSharePath|Shared folder name for Witness|
|windowsImagePublisher|The name of the pulisher of the AD and Witness Image|
|windowsImageOffer|The Offer Name for the Image used by AD and Witness VMs|
|windowsImageSKU|The Image SKU for the AD and Witness Image|
|sqlImagePublisher|The name of the pulisher of the SQL Image|
|sqlImageOffer|The Offer Name for the Image used by SQL|
|sqlImageSKU|The Image SKU for the SQL Image|
|windowsDiskSize|The size of the VHD allocated for AD and Witness VMs Data Disk|
|sqlDiskSize|The size of the VHD allocated for SQL VMs Data and Log Disks|
