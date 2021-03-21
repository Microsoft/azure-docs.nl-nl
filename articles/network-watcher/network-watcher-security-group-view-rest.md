---
title: Netwerk beveiliging analyseren-beveiligings groep weer geven-Azure REST API
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u met de Azure REST API een beveiliging van virtuele machines kunt analyseren met een beveiligings groep.
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
ms.openlocfilehash: 2efd3e9c9ca97ea3d94b03bd5e440cd24d5da5da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960577"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analyseer de beveiliging van uw virtuele machine met de weer gave van de beveiligings groep met REST API

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure-CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> De API voor de weer gave van beveiligings groepen wordt niet meer onderhouden en zal binnenkort worden afgeschaft. Gebruik de [functie effectief beveiligings regels](./network-watcher-security-group-view-overview.md) die dezelfde functionaliteit biedt. 

De weer gave beveiligings groep retourneert geconfigureerde en efficiënte netwerk beveiligings regels die worden toegepast op een virtuele machine. Deze mogelijkheid is nuttig bij het controleren en diagnosticeren van netwerk beveiligings groepen en-regels die zijn geconfigureerd op een virtuele machine om ervoor te zorgen dat verkeer correct wordt toegestaan of geweigerd. In dit artikel laten we zien hoe u de effectief en toegepaste beveiligings regels kunt ophalen voor een virtuele machine met behulp van REST API


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voordat u begint

In dit scenario roept u de Network Watcher rest API aan om de weer gave van de beveiligings groep voor een virtuele machine op te halen. ARMclient wordt gebruikt om de REST API aan te roepen met behulp van Power shell. ARMClient is in Choco lade op [ARMClient op Choco lade](https://chocolatey.org/packages/ARMClient) gevonden

In dit scenario wordt ervan uitgegaan dat u de stappen in [Create a Network Watcher](network-watcher-create.md) voor het maken van een Network Watcher, al hebt gevolgd. In het scenario wordt ervan uitgegaan dat er een resource groep met een geldige virtuele machine bestaat om te worden gebruikt.

## <a name="scenario"></a>Scenario

In het scenario dat in dit artikel wordt behandeld, worden de effectief en toegepaste beveiligings regels voor een bepaalde virtuele machine opgehaald.

## <a name="log-in-with-armclient"></a>Aanmelden met ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Een virtuele machine ophalen

Voer het volgende script uit om een virtuele-machineThe te retour neren met de volgende code variabelen moeten worden geretourneerd:

- **subscriptionId** -de abonnements-id kan ook worden opgehaald met de cmdlet **Get-AzSubscription** .
- **resourceGroupName** : de naam van een resource groep die virtuele machines bevat.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

De informatie die nodig is, is de **id** onder het type `Microsoft.Compute/virtualMachines` in antwoord, zoals wordt weer gegeven in het volgende voor beeld:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>De weer gave van de beveiligings groep voor de virtuele machine ophalen

In het volgende voor beeld wordt de weer gave van de beveiligings groep van een specifieke virtuele machine opgevraagd. De resultaten van dit voor beeld kunnen worden gebruikt om te vergelijken met de regels en beveiliging die zijn gedefinieerd door de herkomst om te zoeken naar configuratie drift.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Bekijk het antwoord

Het volgende voor beeld is het antwoord dat is geretourneerd door de voor gaande opdracht. De resultaten tonen alle effectief en toegepaste beveiligings regels op de virtuele machine, onderverdeeld in groepen **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** en **EffectiveSecurityRules**.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

Ga naar [controle netwerk beveiligings groepen (NSG) met Network Watcher](network-watcher-security-group-view-powershell.md) voor meer informatie over het automatiseren van de validatie van netwerk beveiligings groepen.