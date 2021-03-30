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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680771"
---
De Power shell **-cmdlet Get-AzPeeringLocation** retourneert een lijst met peering locaties met de verplichte para meter `Kind` , die u in latere stappen zult gebruiken.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Directe peering-locaties bevatten de volgende velden:
* PeeringLocation 
* Land/regio
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Controleer of u aanwezig bent op de gewenste peering-faciliteit door te verwijzen naar [PeeringDB](https://wwww.peeringdb.com).

In dit voor beeld ziet u hoe u Seattle gebruikt als de peering-locatie voor het maken van een directe peering.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```