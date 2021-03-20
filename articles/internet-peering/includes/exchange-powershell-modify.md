---
title: bestand opnemen
titleSuffix: Azure
description: bestand opnemen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81678674"
---
De volgende wijzigings bewerkingen worden ondersteund voor Exchange-peering:
* Exchange-peering-verbindingen toevoegen.
* Verwijder Exchange peering-verbindingen.
* Een IPv4-of IPv6-sessie toevoegen aan actieve verbindingen.
* Een IPv4-of IPv6-sessie op actieve verbindingen verwijderen.


### <a name="add-exchange-peering-connections"></a>Exchange-peering verbindingen toevoegen

In dit voor beeld wordt beschreven hoe u verbindingen kunt toevoegen aan een bestaande Exchange-peering.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Exchange-peering verbindingen verwijderen

In dit voor beeld wordt beschreven hoe u verbindingen met een bestaande Exchange-peering verwijdert.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Bekijk alle verbindingen en selecteer de verbinding die u wilt verwijderen. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

Voer in de volgende opdracht in plaats van 0 het index nummer in voor de verbinding die u wilt verwijderen.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Een IPv4-of IPv6-sessie toevoegen aan actieve verbindingen

In dit voor beeld wordt beschreven hoe u een IPv6-sessie toevoegt aan een bestaande Exchange-verbinding.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Een IPv4-of IPv6-sessie op actieve verbindingen verwijderen

Het verwijderen van een IPv4-of IPv6-sessie van een bestaande verbinding wordt momenteel niet ondersteund in Power shell. Neem contact op met [micro soft-peering](mailto:peeringexperience@microsoft.com)voor meer informatie.