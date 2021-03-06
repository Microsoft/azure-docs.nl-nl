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
ms.openlocfilehash: bce09fad6ffa169a019628498a686226eff266c7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384971"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Vereisten voor het implementeren van Azure Cloud Services (uitgebreide ondersteuning)

Als u de implementatie van een geslaagde Cloud Services wilt controleren, controleert u de onderstaande stappen en voert u elk item uit voordat u een implementatie uitvoert. 

## <a name="required-service-configuration-cscfg-file-updates"></a>Vereiste updates voor het service configuratie bestand (. cscfg)

### <a name="1-virtual-network"></a>1) Virtual Network
Implementaties van Cloud service (uitgebreide ondersteuning) moeten zich in een virtueel netwerk bestaan. U kunt een virtueel netwerk maken via [Azure Portal](../virtual-network/quick-create-portal.md), [Power shell](../virtual-network/quick-create-powershell.md), de [Azure cli](../virtual-network/quick-create-cli.md) -of [arm-sjabloon](../virtual-network/quick-create-template.md). Naar het virtuele netwerk en subnetten moet ook worden verwezen in de service configuratie (. cscfg) onder de sectie [NetworkConfiguration](schema-cscfg-networkconfiguration.md) . 

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
Verwijder oude Diagnostische instellingen voor elke rol in het service configuratie bestand (. cscfg).

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
```

## <a name="required-service-definition-file-csdef-updates"></a>Vereiste updates voor het service definitie bestand (. csdef)

> [!NOTE]
> Wijzigingen in het service definitie bestand (. csdef) vereist dat het pakket bestand (. cspkg) opnieuw wordt gegenereerd. Maak uw. cspkg-bericht en verpakt het opnieuw met de volgende wijzigingen in het. csdef-bestand om de meest recente instellingen voor uw Cloud service op te halen

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
> Als u een lijst met beschik bare grootten wilt ophalen, raadpleegt u [resource sku's-List](/rest/api/compute/resourceskus/list) en past u de volgende filters toe: <br>
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
Implementaties die de oude invoeg toepassingen voor diagnostische gegevens hebben gebruikt, moeten de instellingen voor elke rol verwijderen uit het bestand met de service definitie (. csdef)

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

## <a name="key-vault-creation"></a>Key Vault maken 

Key Vault wordt gebruikt om certificaten op te slaan die zijn gekoppeld aan Cloud Services (uitgebreide ondersteuning). Voeg de certificaten toe aan Key Vault en verwijs vervolgens naar de certificaat vingerafdrukken in het service configuratie bestand. U moet ook Key Vault ' toegangs beleid ' (in portal) inschakelen voor ' Azure Virtual Machines voor implementatie ' zodat Cloud Services (uitgebreide ondersteunings resource) een certificaat kan ophalen dat is opgeslagen als geheimen van Key Vault. U kunt een sleutel kluis maken in de [Azure Portal](../key-vault/general/quick-create-portal.md) of met behulp van [Power shell](../key-vault/general/quick-create-powershell.md). De sleutel kluis moet worden gemaakt in dezelfde regio en hetzelfde abonnement als de Cloud service. Zie [certificaten met Azure Cloud Services gebruiken (uitgebreide ondersteuning)](certificates-and-key-vault.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen 
- Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Ga naar de [opslag plaats voor beelden van Cloud Services (uitgebreide ondersteuning)](https://github.com/Azure-Samples/cloud-services-extended-support)
