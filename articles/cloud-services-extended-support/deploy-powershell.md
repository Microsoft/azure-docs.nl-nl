---
title: Een Cloud service implementeren (uitgebreide ondersteuning)-Power shell
description: Een Cloud service (uitgebreide ondersteuning) implementeren met behulp van Power shell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 8bfa7c164f5b974a8cf8974b3ff346f3401dd218
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880217"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Een Cloud service (uitgebreide ondersteuning) implementeren met behulp van Azure PowerShell

In dit artikel wordt beschreven hoe u de `Az.CloudService` Power shell-module gebruikt om Cloud Services (uitgebreide ondersteuning) te implementeren in azure met meerdere rollen (webrole en WorkerRole) en de extensie extern bureau blad. 

> [!IMPORTANT]
> Cloud Services (uitgebreide ondersteuning) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning) en maak de bijbehorende resources. 

## <a name="deploy-a-cloud-services-extended-support"></a>Een Cloud Services implementeren (uitgebreide ondersteuning)
1. Installeren AZ. Microsoftcompute Power shell-module  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Een nieuwe resourcegroep maken. Deze stap is optioneel als u een bestaande resource groep gebruikt.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Maak een opslag account en een container die wordt gebruikt voor het opslaan van het Cloud service package (. cspkg) en de service configuratie (. cscfg)-bestanden. U moet een unieke naam voor de naam van het opslag account gebruiken. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “ContosoContainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Upload uw Cloud service pakket (cspkg) naar het opslag account.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “ContosoContainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “ContosoContainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Upload uw Cloud service configuratie (cscfg) naar het opslag account. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container ContosoContainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “ContosoContainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Maak een virtueel netwerk en subnet. Deze stap is optioneel als u een bestaand netwerk en subnet gebruikt. In dit voor beeld wordt één virtueel netwerk en subnet gebruikt voor Cloud service rollen (webrole en WorkerRole). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Maak een openbaar IP-adres en (optioneel) Stel de DNS-label eigenschap van het open bare IP-adres in. Als u een statisch IP-adres gebruikt, moet er naar worden verwezen als een Gereserveerd IP in het service configuratie bestand.  

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Maak een netwerk profiel object en koppel het open bare IP-adres aan de front-end van het platform dat is gemaakt load balancer.  

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Een sleutelkluis maken. Deze Key Vault wordt gebruikt om certificaten op te slaan die zijn gekoppeld aan de rollen van de Cloud service (uitgebreide ondersteuning). De Key Vault moet zich in dezelfde regio en hetzelfde abonnement bevinden als de Cloud service en een unieke naam hebben. Zie [certificaten met Azure Cloud Services gebruiken (uitgebreide ondersteuning)](certificates-and-key-vault.md)voor meer informatie.

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosoOrg” -Location “East US” 
    ```

10. Het Key Vault toegangs beleid bijwerken en certificaat machtigingen verlenen aan uw gebruikers account. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosoOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    U kunt ook toegangs beleid instellen via ObjectId (dat kan worden verkregen door uit te voeren `Get-AzADUser` ) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. Voor het doel van dit voor beeld voegen we een zelfondertekend certificaat toe aan een Key Vault. De vinger afdruk van het certificaat moet worden toegevoegd in het bestand Cloud service Configuration (. cscfg) voor de implementatie van Cloud service-rollen. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Een besturingssysteem profiel in het geheugen-object maken. BESTURINGSSYSTEEM profiel Hiermee geeft u de certificaten op die zijn gekoppeld aan Cloud service rollen. Dit is hetzelfde certificaat dat u in de vorige stap hebt gemaakt. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Maak een rollen profiel in het geheugen object. Een roltype definieert een specifieke SKU-eigenschappen, zoals naam, capaciteit en laag. Voor dit voor beeld hebben we twee rollen gedefinieerd: frontendRole en backendRole. De informatie van het rolinstantie moet overeenkomen met de functie configuratie die is gedefinieerd in het configuratie bestand (cscfg) en het bestand met de service definitie (csdef). 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. Beschrijving Maak een extensie profiel in het geheugen object dat u wilt toevoegen aan uw Cloud service. Voor dit voor beeld gaan we RDP-uitbrei ding toevoegen. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $extension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "ContosSA" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
15. Beschrijving Tags definiëren als een Power shell-Hash-tabel die u wilt toevoegen aan uw Cloud service. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Maak Cloud service-implementatie met behulp van profiel objecten & SAS-Url's.

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
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).
- Ga naar de [opslag plaats voor beelden van Cloud Services (uitgebreide ondersteuning)](https://github.com/Azure-Samples/cloud-services-extended-support)
