---
title: Een cloudservice implementeren (uitgebreide ondersteuning) - PowerShell
description: Een cloudservice (uitgebreide ondersteuning) implementeren met Behulp van PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a7e4e76aa0619d78a91d766a9a43c0b1a02a48d3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717383"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Een cloudservice implementeren (uitgebreide ondersteuning) met behulp van Azure PowerShell

In dit artikel wordt beschreven hoe u de PowerShell-module gebruikt voor het implementeren van Cloud Services (uitgebreide ondersteuning) in Azure met meerdere rollen (WebRole en WorkerRole) en de extensie extern `Az.CloudService` bureaublad. 

## <a name="before-you-begin"></a>Voordat u begint

Controleer de [implementatievoorwaarden voor Cloud Services](deploy-prerequisite.md) (uitgebreide ondersteuning) en maak de bijbehorende resources. 

## <a name="deploy-a-cloud-services-extended-support"></a>Een Cloud Services implementeren (uitgebreide ondersteuning)
1. Az.CloudService PowerShell-module installeren  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Een nieuwe resourcegroep maken. Deze stap is optioneel als u een bestaande resourcegroep gebruikt.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Maak een opslagaccount en container die worden gebruikt voor het opslaan van de cloudservicepakketbestanden (.cspkg) en serviceconfiguratiebestanden (.cscfg). U moet een unieke naam gebruiken voor de naam van het opslagaccount. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Upload uw Cloud Service-pakket (cspkg) naar het opslagaccount.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Upload uw cloudserviceconfiguratie (cscfg) naar het opslagaccount. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Maak een virtueel netwerk en subnet. Deze stap is optioneel als u een bestaand netwerk en subnet gebruikt. In dit voorbeeld worden één virtueel netwerk en subnet gebruikt voor beide cloudservicerollen (WebRole en WorkerRole). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Maak een openbaar IP-adres en stel de eigenschap DNS-label van het openbare IP-adres in. Cloud Services (uitgebreide ondersteuning) ondersteunt [](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) alleen openbare IP-adressen van basic-SKU's. Openbare IP's van de Standard-SKU werken niet met Cloud Services.
Als u een statisch IP-adres gebruikt, moet u hier naar verwijzen als een Gereserveerd IP in het serviceconfiguratiebestand (.cscfg) 

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Maak een netwerkprofielobject en koppel het openbare IP-adres aan de front-load balancer. Het Azure-platform maakt automatisch een 'klassieke' SKU load balancer resource in hetzelfde abonnement als de cloudserviceresource. De load balancer is een alleen-lezen resource in ARM. Updates voor de resource worden alleen ondersteund via de implementatiebestanden van de cloudservice (.cscfg & .csdef)

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Een sleutelkluis maken. Deze Key Vault worden gebruikt voor het opslaan van certificaten die zijn gekoppeld aan de cloudservicerollen (uitgebreide ondersteuning). De Key Vault moeten zich in dezelfde regio en hetzelfde abonnement bevinden als de cloudservice en een unieke naam hebben. Zie Certificaten gebruiken [met Azure Cloud Services (uitgebreide ondersteuning) voor meer informatie.](certificates-and-key-vault.md)

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Werk het Key Vault toegangsbeleid bij en verleen certificaatmachtigingen aan uw gebruikersaccount. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    U kunt ook toegangsbeleid instellen via ObjectId (dat kan worden verkregen door uit te `Get-AzADUser` voeren) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. In dit voorbeeld voegen we een zelf ondertekend certificaat toe aan een Key Vault. De vingerafdruk van het certificaat moet worden toegevoegd in het cloudserviceconfiguratiebestand (.cscfg) voor implementatie in cloudservicerollen. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Maak een besturingssysteemprofiel in het geheugenobject. Besturingssysteemprofiel geeft de certificaten aan die zijn gekoppeld aan cloudservicerollen. Dit is hetzelfde certificaat dat u in de vorige stap hebt gemaakt. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Maak een rolprofiel in het geheugenobject. Rolprofiel definieert specifieke eigenschappen van een SKU voor rollen, zoals naam, capaciteit en laag. Voor dit voorbeeld hebben we twee rollen gedefinieerd: frontendRole en backendRole. Informatie over rolprofiel moet overeenkomen met de rolconfiguratie die is gedefinieerd in het configuratiebestand (cscfg) en het csdef-bestand (servicedefinitie). 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. (Optioneel) Maak een extensieprofiel in het geheugenobject dat u aan uw cloudservice wilt toevoegen. Voor dit voorbeeld voegen we de RDP-extensie toe. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    Houd er rekening mee dat configFile alleen PublicConfig-tags mag bevatten en als volgt een naamruimte moet bevatten:
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. (Optioneel) Definieer Tags als PowerShell-hashtabel die u aan uw cloudservice wilt toevoegen. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Maak cloudservice-implementatie met behulp van profielobjecten & SAS-URL's.

    ```powershell
    $cloudService = New-AzCloudService ` 
    -Name “ContosoCS” ` 
    -ResourceGroupName “ContosOrg” ` 
    -Location “East US” ` 
    -PackageUrl $cspkgUrl ` 
    -ConfigurationUrl $cscfgUrl ` 
    -UpgradeMode 'Auto' ` 
    -RoleProfile $roleProfile ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile `
    -Tag $tag 
    ```

## <a name="next-steps"></a>Volgende stappen 
- Bekijk [veelgestelde vragen over](faq.md) Cloud Services (uitgebreide ondersteuning).
- Implementeer een cloudservice (uitgebreide ondersteuning) met behulp [van Azure Portal,](deploy-portal.md) [PowerShell,](deploy-powershell.md) [Sjabloon](deploy-template.md) [of Visual Studio.](deploy-visual-studio.md)
- Ga naar [de opslagplaats Cloud Services voorbeelden voor ondersteuning (uitgebreide ondersteuning)](https://github.com/Azure-Samples/cloud-services-extended-support)
