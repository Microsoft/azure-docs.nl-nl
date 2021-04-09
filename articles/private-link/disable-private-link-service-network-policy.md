---
title: 'Netwerk beleid uitschakelen voor het IP-adres van de Azure Private Link service-bron '
description: Meer informatie over het uitschakelen van netwerk beleid voor persoonlijke Azure-koppelingen
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7528d12dcbfba09b4dae4ac07a5c683a7c11bd88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99548796"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Netwerk beleid voor de bron-IP van de persoonlijke koppelings service uitschakelen

Als u een IP-adres van een bron wilt kiezen voor uw persoonlijke koppelings service, is een expliciete instelling voor uitschakelen `privateLinkServiceNetworkPolicies` vereist in het subnet. Deze instelling is alleen van toepassing op het specifieke privé-IP-adres dat u hebt gekozen als bron-IP van de privé koppelings service. Voor andere bronnen in het subnet wordt de toegang beheerd op basis van de definitie van beveiligings regels voor netwerk beveiligings groepen (NSG). 
 
Wanneer u de portal gebruikt om een persoonlijke koppelings service te maken, wordt deze instelling automatisch uitgeschakeld als onderdeel van het proces maken. Voor implementaties die gebruikmaken van een Azure-client (Power shell, CLI of sjablonen), is een extra stap vereist om deze eigenschap te wijzigen. U kunt het beleid uitschakelen met behulp van de Cloud shell van de Azure Portal, of lokale installaties van Azure PowerShell, Azure CLI of Azure Resource Manager-sjablonen gebruiken.  
 
Volg de onderstaande stappen om het netwerk beleid van de persoonlijke koppelings service voor een virtueel netwerk met de naam *myVirtualNetwork* uit te scha kelen met een *standaard* subnet dat wordt gehost in een resource groep met de naam *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken
In deze sectie wordt beschreven hoe u beleid voor privé-eind punten van subnet kunt uitschakelen met behulp van Azure PowerShell.
Vervang in de code ' default ' door de naam van het virtuele subnet.

```azurepowershell
$virtualSubnetName = "default"
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $virtualSubnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Azure CLI gebruiken
In deze sectie wordt beschreven hoe u beleid voor privé-eind punten van subnet uitschakelen met behulp van Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Een sjabloon gebruiken
In deze sectie wordt beschreven hoe u beleid voor privé-eind punten van subnet kunt uitschakelen met Azure Resource Manager sjabloon.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [persoonlijk Azure-eind punt](private-endpoint-overview.md)
 
