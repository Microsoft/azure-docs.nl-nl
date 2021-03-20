---
title: NSG-controle automatiseren-beveiligings groeps weergave
titleSuffix: Azure Network Watcher
description: Op deze pagina vindt u instructies voor het configureren van de controle van een netwerk beveiligings groep
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
ms.openlocfilehash: 177215775c9e83286aa98872eed0ab211a8f36ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94948745"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>NSG auditing automatiseren met de beveiligings groeps weergave van Azure Network Watcher

Klanten worden vaak geconfronteerd met de uitdaging van het controleren van de beveiligings postuur van hun infra structuur. Deze uitdaging is niet anders voor hun virtuele machines in Azure. Het is belang rijk dat u een soortgelijk beveiligings profiel gebruikt op basis van de regels voor de netwerk beveiligings groep (NSG). Met de weer gave beveiligings groep kunt u nu de lijst met regels die zijn toegepast op een virtuele machine in een NSG ophalen. U kunt een Golden NSG-beveiligings profiel definiëren en de weer gave van de beveiligings groep op een wekelijkse uitgebracht initiëren en de uitvoer vergelijken met het profiel Golden en een rapport maken. Op deze manier kunt u alle Vm's die niet voldoen aan het vereiste beveiligings profiel, gemakkelijk identificeren.

Zie [overzicht van netwerk beveiliging](../virtual-network/network-security-groups-overview.md)als u niet bekend bent met netwerk beveiligings groepen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voordat u begint

In dit scenario vergelijkt u een bekende goede basis lijn met de resultaten van de weer gave van de beveiligings groep die voor een virtuele machine worden geretourneerd.

In dit scenario wordt ervan uitgegaan dat u de stappen in [Create a Network Watcher](network-watcher-create.md) voor het maken van een Network Watcher, al hebt gevolgd. In het scenario wordt ervan uitgegaan dat er een resource groep met een geldige virtuele machine bestaat om te worden gebruikt.

## <a name="scenario"></a>Scenario

In het scenario dat in dit artikel wordt behandeld, wordt de beveiligings groep weer gegeven voor een virtuele machine.

In dit scenario gaat u als volgt te werkt:

- Een bekende goede regel set ophalen
- Een virtuele machine met rest API ophalen
- De weer gave van de beveiligings groep voor de virtuele machine ophalen
- Antwoord evalueren

## <a name="retrieve-rule-set"></a>Regelset ophalen

De eerste stap in dit voor beeld is het werken met een bestaande basis lijn. In het volgende voor beeld wordt een JSON geëxtraheerd uit een bestaande netwerk beveiligings groep met behulp van de `Get-AzNetworkSecurityGroup` cmdlet die als basis lijn voor dit voor beeld wordt gebruikt.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Regel instellingen converteren naar Power shell-objecten

In deze stap lezen we een JSON-bestand dat eerder is gemaakt met de regels die naar verwachting worden opgenomen in de netwerk beveiligings groep voor dit voor beeld.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Network Watcher ophalen

De volgende stap bestaat uit het ophalen van het Network Watcher-exemplaar. De `$networkWatcher` variabele wordt door gegeven aan de `AzNetworkWatcherSecurityGroupView` cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Een virtuele machine ophalen

Een virtuele machine is vereist om de cmdlet uit te voeren `Get-AzNetworkWatcherSecurityGroupView` . In het volgende voor beeld wordt een VM-object opgehaald.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Weer gave van beveiligings groep ophalen

De volgende stap is het ophalen van het resultaat van de beveiligings groep weer geven. Dit resultaat wordt vergeleken met de JSON van de basis lijn die eerder is weer gegeven.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>De resultaten analyseren

Het antwoord wordt gegroepeerd op netwerk interfaces. De verschillende soorten regels die worden geretourneerd, zijn effectief en standaard beveiligings regels. Het resultaat wordt verder onderverdeeld in de manier waarop het wordt toegepast, hetzij op een subnet of een virtuele NIC.

Met het volgende Power shell-script worden de resultaten van de weer gave van de beveiligings groep vergeleken met een bestaande uitvoer van een NSG. Het volgende voor beeld is een eenvoudig voor beeld van hoe de resultaten kunnen worden vergeleken met de `Compare-Object` cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Het volgende voor beeld is het resultaat. U ziet dat twee van de regels in de eerste regelset niet aanwezig waren in de vergelijking.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Volgende stappen

Als de instellingen zijn gewijzigd, raadpleegt u [netwerk beveiligings groepen beheren](../virtual-network/manage-network-security-group.md) om de netwerk beveiligings groep en beveiligings regels te traceren die in kwestie zijn.