---
title: Netwerk beleid voor privé-eind punten in azure uitschakelen
description: Meer informatie over het uitschakelen van netwerk beleid voor privé-eind punten.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: a4c04033f3fb5ff523f0d80bb6c978955f4c9ec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84737509"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Netwerk beleid voor privé-eind punten uitschakelen

Netwerkbeleid zoals netwerkbeveiligingsgroepen (NSG) wordt niet ondersteund voor privé-eindpunten. Voor het implementeren van een privé-eindpunt op een bepaald subnet is een expliciete instelling uitschakelen vereist voor dat subnet. Deze instelling is alleen van toepassing op het persoonlijke eind punt. Voor andere bronnen in het subnet wordt de toegang beheerd op basis van de definitie van beveiligings regels voor netwerk beveiligings groepen (NSG). 
 
Wanneer u de portal gebruikt voor het maken van een persoonlijk eind punt, wordt deze instelling automatisch uitgeschakeld als onderdeel van het proces maken. Voor de implementatie met behulp van andere clients is een extra stap vereist om deze instelling te wijzigen. U kunt de instelling uitschakelen met Cloud shell vanuit de Azure Portal, of lokale installaties van Azure PowerShell, Azure CLI of Azure Resource Manager-sjablonen gebruiken.  
 
In de volgende voor beelden wordt beschreven hoe u uitschakelen `PrivateEndpointNetworkPolicies` voor een virtueel netwerk met de naam *myVirtualNetwork* met een *standaard* subnet dat wordt gehost in een resource groep met de naam *myResourceGroup*.

## <a name="using-azure-powershell"></a>Azure PowerShell gebruiken
In deze sectie wordt beschreven hoe u beleid voor privé-eind punten van subnet kunt uitschakelen met behulp van Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Azure CLI gebruiken
In deze sectie wordt beschreven hoe u beleid voor privé-eind punten van subnet uitschakelen met behulp van Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
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
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [persoonlijk Azure-eind punt](private-endpoint-overview.md)
 
