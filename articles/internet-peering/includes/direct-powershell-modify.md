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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680799"
---
In deze sectie wordt beschreven hoe u de volgende wijzigings bewerkingen uitvoert voor directe peering:

* Directe peering-verbindingen toevoegen.
* Verwijder directe peering-verbindingen.
* De band breedte van actieve verbindingen upgraden of verlagen.
* IPv4-of IPv6-sessies toevoegen aan actieve verbindingen.
* IPv4-of IPv6-sessies op actieve verbindingen verwijderen.

### <a name="add-direct-peering-connections"></a>Directe peering-verbindingen toevoegen

In dit voor beeld wordt beschreven hoe u verbindingen kunt toevoegen aan bestaande directe peering.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Directe peering-verbindingen verwijderen

Het verwijderen van een verbinding wordt momenteel niet ondersteund in Power shell. Neem contact op met [micro soft-peering](mailto:peeringexperience@microsoft.com)voor meer informatie.

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>De band breedte van actieve verbindingen upgraden of verlagen

In dit voor beeld wordt beschreven hoe u 10 Gbps kunt toevoegen aan een bestaande directe verbinding.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>IPv4-of IPv6-sessies toevoegen aan actieve verbindingen

In dit voor beeld wordt beschreven hoe u een IPv6-sessie kunt toevoegen aan een bestaande directe verbinding met alleen een IPv4-sessie. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>IPv4-of IPv6-sessies voor actieve verbindingen verwijderen

Het verwijderen van een IPv4-of IPv6-sessie van een bestaande verbinding wordt momenteel niet ondersteund in Power shell. Neem contact op met [micro soft-peering](mailto:peeringexperience@microsoft.com)voor meer informatie.