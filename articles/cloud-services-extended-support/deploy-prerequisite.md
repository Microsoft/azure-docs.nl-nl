---
title: Vereisten voor het implementeren van Azure Cloud Services (uitgebreide ondersteuning)
description: Vereisten voor het implementeren van Azure Cloud Services (uitgebreide ondersteuning)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: f112d0e96c6ff0caf3c5e3762304158f70963f14
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787026"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Vereisten voor het implementeren van Azure Cloud Services (uitgebreide ondersteuning)

> [!IMPORTANT]
> Cloud Services (uitgebreide ondersteuning) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Als u de implementatie van een geslaagde Cloud Services wilt controleren, controleert u de onderstaande stappen en voert u elk item uit voordat u een implementatie uitvoert. 

## <a name="register-the-cloudservices-feature"></a>De functie CloudServices registreren
Registreer de functie voor uw abonnement. Het volt ooien van de registratie kan enkele minuten duren. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Controleer de status van de registratie met behulp van het volgende:  
```powershell
Get-AzProviderFeature 

#Sample output
FeatureName               ProviderName      RegistrationState
CloudServices           Microsoft.Compute    Registered
```

## <a name="required-service-configuration-cscfg-file-updates"></a>Vereiste updates voor het service configuratie bestand (. cscfg)

### <a name="1-virtual-network"></a>1) Virtual Network
Implementaties van Cloud service (uitgebreide ondersteuning) moeten zich in een virtueel netwerk bestaan. U kunt een virtueel netwerk maken via [Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal), [Power shell](https://docs.microsoft.com/azure/virtual-network/quick-create-powershell), de [Azure cli](https://docs.microsoft.com/azure/virtual-network/quick-create-cli) -of [arm-sjabloon](https://docs.microsoft.com/azure/virtual-network/quick-create-template). Naar het virtuele netwerk en subnetten moet ook worden verwezen in de service configuratie (. cscfg) onder de sectie [NetworkConfiguration](schema-cscfg-networkconfiguration.md) . 

Voor een virtueel netwerk dat deel uitmaakt van dezelfde resource groep als de Cloud service, is alleen het verwijzen naar de naam van het virtuele apparaat in het service configuratie bestand (. cscfg) voldoende. Als het virtuele netwerk en de Cloud service zich in twee verschillende resource groepen bevinden, moet de volledige Azure Resource Manager-ID van het virtuele netwerk worden opgegeven in het bestand met de service configuratie (. cscfg).
 
#### <a name="virtual-network-located-in-same-resource-group"></a>Virtual Network zich in dezelfde resource groep bevinden
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>Virtueel netwerk bevindt zich in een andere resource groep
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2) Verwijder de oude invoeg toepassingen

Oude instellingen voor extern bureau blad verwijderen uit het service configuratie bestand (. cscfg).  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```

## <a name="required-service-definition-file-csdef-updates"></a>Vereiste updates voor het service definitie bestand (. csdef)

### <a name="1-virtual-machine-sizes"></a>1) grootte van virtuele machines
De volgende grootten zijn afgeschaft in Azure Resource Manager. Als u ze echter wilt blijven gebruiken, moet u de `vmsize` naam bijwerken met de bijbehorende Azure Resource Manager naam Conventie.  

| Naam van vorige grootte | Naam van bijgewerkte grootte | 
|---|---|
| ExtraSmall | Standard_A0 | 
| Klein | Standard_A1 |
| Normaal | Standard_A2 | 
| Groot | Standard_A3 | 
| ExtraLarge | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| A7 | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 Dit wordt bijvoorbeeld `<WorkerRole name="WorkerRole1" vmsize="Medium"` `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"` .
 
> [!NOTE]
> Als u een lijst met beschik bare grootten wilt ophalen, raadpleegt u [resource sku's-List](https://docs.microsoft.com/rest/api/compute/resourceskus/list) en past u de volgende filters toe: <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) oude extern bureau blad-invoeg toepassingen verwijderen
Voor implementaties die de oude extern bureau blad-invoeg toepassingen hebben gebruikt, moeten de modules worden verwijderd uit het bestand met de service definitie (. csdef) en de bijbehorende certificaten. 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```

## <a name="key-vault-creation"></a>Key Vault maken 

Key Vault wordt gebruikt om certificaten op te slaan die zijn gekoppeld aan Cloud Services (uitgebreide ondersteuning). Voeg de certificaten toe aan Key Vault en verwijs vervolgens naar de certificaat vingerafdrukken in het service configuratie bestand. U moet ook Key Vault inschakelen voor de juiste machtigingen, zodat Cloud Services (uitgebreide ondersteunings resource) het certificaat kan ophalen dat is opgeslagen als geheimen van Key Vault. Key Vault kunnen worden gemaakt via [Azure Portal](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal)en  [Power shell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell). De Key Vault moet in dezelfde regio en hetzelfde abonnement worden gemaakt als de Cloud service. Zie [certificaten met Azure Cloud Services gebruiken (uitgebreide ondersteuning)](certificates-and-key-vault.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen 
- Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
