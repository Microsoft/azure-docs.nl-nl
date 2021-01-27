---
title: 'Azure PowerShell-voorbeelden: een Azure-cloudservice (uitgebreide ondersteuning) maken'
description: Voorbeeld scripts voor het maken van implementaties van Azure Cloud service (uitgebreide ondersteuning)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 61c8924b5dba37e3fc61da4710b9bcdad34ebeca
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881531"
---
# <a name="create-a-new-azure-cloud-service-extended-support"></a>Een nieuwe Azure-Cloud service maken (uitgebreide ondersteuning)
Deze voor beelden beslaan verschillende manieren om een nieuwe implementatie van Azure Cloud service (uitgebreide ondersteuning) te maken.

## <a name="create-new-cloud-service-with-single-role"></a>Nieuwe Cloud service met één rol maken

```powershell
# Create role profile object
$role = New-AzCloudServiceCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2

# Create network profile object
$publicIp = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp
$feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIp.Id
$loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig

# Read Configuration File
$cscfgFile = "<Path to cscfg configuration file>"
$cscfgContent = Get-Content $cscfgFile | Out-String

# Create Cloud Service
$cloudService = New-AzCloudService                                              `
-Name ContosoCS                                               `
-ResourceGroupName ContosOrg                                  `
-Location EastUS                                              `
-PackageUrl "https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"    `
-Configuration $cscfgContent                                  `
-UpgradeMode 'Auto'                                           `
-RoleProfileRole $role                                       `
-NetworkProfileLoadBalancerConfiguration $loadBalancerConfig
```


## <a name="create-new-cloud-service-with-single-role-and-remote-desktop-extension"></a>Nieuwe Cloud service maken met de extensie voor één rol en extern bureau blad

```powershell
# Create role profile object
$role = New-AzCloudServiceCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2

# Create network profile object
$publicIp = Get-AzPublicIpAddress -ResourceGroupName ContosoOrg -Name ContosIp
$feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIp.Id
$loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig

# Create RDP extension object
$credential = Get-Credential
$expiration = (Get-Date).AddYears(1)
$extension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1'

# Read Configuration File
$cscfgFile = "<Path to cscfg configuration file>"
$cscfgContent = Get-Content $cscfgFile | Out-String

# Create Cloud Service
$cloudService = New-AzCloudService                                              `
-Name ContosoCS                                               `
-ResourceGroupName ContosOrg                                  `
-Location EastUS                                              `
-PackageUrl "https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"    `
-Configuration $cscfgContent                                  `
-UpgradeMode 'Auto'                                           `
-RoleProfileRole $role                                        `
-NetworkProfileLoadBalancerConfiguration $loadBalancerConfig  `
-ExtensionProfileExtension $extension
```

## <a name="create-a-cloud-service-with-a-single-role-and-windows-azure-diagnostics-extension"></a>Een Cloud service maken met een uitbrei ding voor één rol en Windows-Azure Diagnostics

```PowerShell
# Create role profile object
$role = New-AzCloudServiceCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2

# Create network profile object
$publicIp = Get-AzPublicIpAddress -ResourceGroupName ContosoOrg -Name ContosIp
$feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIp.Id
$loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig

# Create WAD extension object
$storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "ContosSA"
$configFile = "<WAD configuration file path>"
$extension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "ContosSA" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true

# Read Configuration File
$cscfgFile = "<Path to cscfg configuration file>"
$cscfgContent = Get-Content $cscfgFile | Out-String

## Create Cloud Service
$cloudService = New-AzCloudService                                              `
-Name ContosoCS                                               `
-ResourceGroupName ContosOrg                                  `
-Location EastUS                                              `
-PackageUrl "https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"    `
-Configuration $cscfgContent                                  `
-UpgradeMode 'Auto'                                           `
-RoleProfileRole $role                                        `
-NetworkProfileLoadBalancerConfiguration $loadBalancerConfig  `
-ExtensionProfileExtension $extension
```
## <a name="create-new-cloud-service-with-single-role-and-certificate-from-key-vault"></a>Maak een nieuwe Cloud service met één rol en certificaat van Key Vault

```powershell
# Create role profile object
$role = New-AzCloudServiceCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2

# Create OS profile object
$keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault
$certificate=Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert
$secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId

# Create network profile object
$publicIp = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp
$feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIp.Id
 $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig

# Read Configuration File
$cscfgFile = "<Path to cscfg configuration file>"
$cscfgContent = Get-Content $cscfgFile | Out-String

# Create Cloud Service
$cloudService = New-AzCloudService                                              `
-Name ContosoCS                                               `
-ResourceGroupName ContosOrg                                  `
-Location EastUS                                              `
-PackageUrl "https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"    `
-Configuration $cscfgContent                                  `
-UpgradeMode 'Auto'                                           `
-RoleProfileRole $role                                        `
-NetworkProfileLoadBalancerConfiguration $loadBalancerConfig  `
-OSProfileSecret $secretGroup
```

## <a name="create-new-cloud-service-with-multiple-roles-and-extensions"></a>Nieuwe Cloud service maken met meerdere rollen en uitbrei dingen

```powershell
# Create role profile object
 $role1 = New-AzCloudServiceCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2
 $role2 = New-AzCloudServiceCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2
 $roles = @($role1, $role2)

# Create network profile object
 $publicIp = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp
 $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIp.Id
 $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig

# Create RDP extension object
 $credential = Get-Credential
 $expiration = (Get-Date).AddYears(1)
 $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1'

# Create Geneva extension object
 $genevaExtension = New-AzCloudServiceExtensionObject -Name GenevaExtension -Publisher Microsoft.Azure.Geneva -Type GenevaMonitoringPaaS -TypeHandlerVersion "2.14.0.2"
 $extensions = @($rdpExtension, $genevaExtension)

# Add tags
$tag=@{"Owner" = "Contoso"}

# Read Configuration File
$cscfgFile = "<Path to cscfg configuration file>"
$cscfgContent = Get-Content $cscfgFile | Out-String

# Create Cloud Service
$cloudService = New-AzCloudService                                              `
-Name ContosoCS                                               `
-ResourceGroupName ContosOrg                                  `
-Location EastUS                                              `
-PackageUrl "https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"    `
-Configuration $cscfgContent                                  `
-UpgradeMode 'Auto'                                           `
-RoleProfileRole $roles                                       `
-NetworkProfileLoadBalancerConfiguration $loadBalancerConfig  `
-ExtensionProfileExtension $extensions                        `
-Tag $tag
```

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van azure Cloud Services (uitgebreide ondersteuning)](overview.md)voor meer informatie over Azure Cloud Services (uitgebreide ondersteuning).
- Ga naar de [opslag plaats voor beelden van Cloud Services (uitgebreide ondersteuning)](https://github.com/Azure-Samples/cloud-services-extended-support)
