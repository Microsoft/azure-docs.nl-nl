---
title: Netwerk beveiliging analyseren met de weer gave beveiligings groep-Azure CLI
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u Azure CLI gebruikt voor het analyseren van de beveiliging van virtuele machines met een beveiligings groep.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: b3aa963c4da7802a9db714f25e7b544b3a132d4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94948643"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>De beveiliging van uw virtuele machine analyseren met de weer gave van de beveiligings groep met behulp van Azure CLI

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure-CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> De API voor de weer gave van beveiligings groepen wordt niet meer onderhouden en zal binnenkort worden afgeschaft. Gebruik de [functie effectief beveiligings regels](./network-watcher-security-group-view-overview.md) die dezelfde functionaliteit biedt.


De weer gave beveiligings groep retourneert geconfigureerde en efficiënte netwerk beveiligings regels die worden toegepast op een virtuele machine. Deze mogelijkheid is nuttig bij het controleren en diagnosticeren van netwerk beveiligings groepen en-regels die zijn geconfigureerd op een virtuele machine om ervoor te zorgen dat verkeer correct wordt toegestaan of geweigerd. In dit artikel laten we zien hoe u de geconfigureerde en efficiënte beveiligings regels kunt ophalen voor een virtuele machine met behulp van Azure CLI

Als u de stappen in dit artikel wilt uitvoeren, moet u [de Azure-opdracht regel interface voor Mac, Linux en Windows (CLI) installeren](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Voordat u begint

In dit scenario wordt ervan uitgegaan dat u de stappen in [Create a Network Watcher](network-watcher-create.md) voor het maken van een Network Watcher, al hebt gevolgd.

## <a name="scenario"></a>Scenario

In het scenario dat in dit artikel wordt behandeld, worden de geconfigureerde en efficiënte beveiligings regels voor een bepaalde virtuele machine opgehaald.

## <a name="get-a-vm"></a>Een virtuele machine ophalen

Een virtuele machine is vereist om de cmdlet uit te voeren `vm list` . Met de volgende opdracht worden de virtuele machines in een resource groep weer gegeven:

```azurecli
az vm list -resource-group resourceGroupName
```

Wanneer u de virtuele machine weet, kunt u de `vm show` cmdlet gebruiken om de resource-id op te halen:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Weer gave van beveiligings groep ophalen

De volgende stap is het ophalen van het resultaat van de beveiligings groep weer geven.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>De resultaten weer geven

Het volgende voor beeld is een kortere reactie op de geretourneerde resultaten. De resultaten tonen alle effectief en toegepaste beveiligings regels op de virtuele machine, onderverdeeld in groepen **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** en **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

Ga naar [controle netwerk beveiligings groepen (NSG) met Network Watcher](network-watcher-nsg-auditing-powershell.md) voor meer informatie over het automatiseren van de validatie van netwerk beveiligings groepen.

Meer informatie over de beveiligings regels die worden toegepast op uw netwerk bronnen vindt u in [overzicht van beveiligings groepen weer geven](network-watcher-security-group-view-overview.md)