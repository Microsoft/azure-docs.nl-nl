---
title: Rolling over een Azure Service Fabric-cluster certificaat
description: Meer informatie over het oprollen van een Service Fabric cluster certificaat dat wordt geïdentificeerd door de algemene naam van het certificaat.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 65ea4f463073c472ac6a31e62dcfdfd11cb28cc5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88853356"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Hand matig overschakelen op een Service Fabric cluster certificaat
Wanneer een Service Fabric cluster certificaat bijna verloopt, moet u het certificaat bijwerken.  Certificaat overschakeling is eenvoudig als het cluster is ingesteld op het [gebruik van certificaten op basis van algemene naam](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (in plaats van een vinger afdruk).  Vraag een nieuw certificaat aan bij een certificerings instantie met een nieuwe verval datum.  Zelfondertekende certificaten bieden geen ondersteuning voor productie Service Fabric clusters, om certificaten op te nemen die zijn gegenereerd tijdens de werk stroom voor het maken van een Azure Portal cluster. Het nieuwe certificaat moet dezelfde algemene naam hebben als het oudere certificaat. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Service Fabric cluster gebruikt het gedeclareerde certificaat automatisch met een verdere verval datum. Wanneer er meer dan één certificaat voor validatie op de host is geïnstalleerd. Een best practice is het gebruik van een resource manager-sjabloon voor het inrichten van Azure-resources. Voor een niet-productie omgeving kunt u het volgende script gebruiken om een nieuw certificaat te uploaden naar een sleutel kluis en vervolgens het certificaat installeren op de schaalset voor virtuele machines: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($certConfig)

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Reken bewerkingen voor virtuele-machine schaal sets ondersteunen niet dezelfde resource-id voor twee afzonderlijke geheimen, omdat elk geheim een unieke versie van een bron is. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [cluster beveiliging](service-fabric-cluster-security.md).
* [Cluster certificaten bijwerken en beheren](service-fabric-cluster-security-update-certs-azure.md)
