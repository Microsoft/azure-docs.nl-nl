---
title: Container installatie kopie opslag
description: Meer informatie over hoe uw container installatie kopieën en andere artefacten worden opgeslagen in Azure Container Registry, met inbegrip van beveiliging, redundantie en capaciteit.
ms.topic: article
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: 4bdffd111273e00b796e45f4e09bfac9ba6713e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036007"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Opslag van container installatie kopieën in Azure Container Registry

Elke [basis-, standaard-en Premium](container-registry-skus.md) Azure container Registry-voor delen van geavanceerde Azure-opslag functies zoals versleuteling op rest voor afbeeldings gegevens beveiliging en geo-redundantie voor de beveiliging van de afbeeldings gegevens. In de volgende secties worden de functies en limieten beschreven van installatie kopie opslag in Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Versleuteling-at-rest

Alle container installatie kopieën en andere artefacten in uw REGI ster worden op rest versleuteld. Azure versleutelt automatisch een afbeelding voordat deze wordt opgeslagen en ontsleutelt deze op het moment dat u of uw toepassingen en services de installatie kopie ophalen. Pas desgewenst een extra versleutelings laag toe met een door de [klant beheerde sleutel](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Geografisch redundante opslag

Voor container registers die in de meeste regio's zijn geïmplementeerd, maakt Azure gebruik van een geografisch redundant opslag schema om te helpen beschermen tegen verlies van uw container installatie kopieën en andere artefacten. Azure Container Registry repliceert uw container installatie kopieën automatisch naar meerdere geografisch externe data centers, waardoor het verlies wordt voor komen als er een regionale opslag fout optreedt.

> [!IMPORTANT]
> * Als er een regionale opslag fout optreedt, kunnen de register gegevens alleen worden hersteld door contact op te nemen met de ondersteuning van Azure. 
> * Als gevolg van de vereisten voor gegevens locatie in Brazilië-zuid en Zuidoost-Azië, worden Azure Container Registry gegevens in deze regio's [alleen op lokale geografische locatie](https://azure.microsoft.com/global-infrastructure/geographies/)opgeslagen. Voor Zuidoost-Azië worden alle gegevens opgeslagen in Singapore. Voor Brazilië-zuid worden alle gegevens opgeslagen in Brazilië. Als de regio is verwijderd vanwege een aanzienlijke nood geval, kan micro soft uw Azure Container Registry gegevens niet herstellen.

## <a name="geo-replication"></a>Geo-replicatie

Overweeg het gebruik van de functie voor [geo-replicatie](container-registry-geo-replication.md) van Premium-registers voor scenario's die nog meer zekerheid voor hoge Beschik baarheid vereisen. Geo-replicatie biedt beveiliging tegen verlies van toegang tot uw REGI ster in het geval van een *totale* regionale fout, niet alleen een opslag fout. Geo-replicatie biedt ook andere voor delen, zoals de opslag van installatie kopieën van het netwerk, voor snellere pushes en pull-bewerkingen in gedistribueerde ontwikkel-of implementatie scenario's.

## <a name="zone-redundancy"></a>Zoneredundantie

Als u een robuust en Maxi maal beschik bare Azure container Registry wilt maken, schakelt u optioneel [zone redundantie](zone-redundancy.md) in in azure-regio's selecteren. Een functie van de service tier Premium maakt gebruik van Azure- [beschikbaarheids zones](../availability-zones/az-overview.md) om uw REGI ster te repliceren naar een minimum van drie afzonderlijke zones in elke ingeschakelde regio. Combi neer geo-replicatie en zone redundantie om zowel de betrouw baarheid als de prestaties van een REGI ster te verbeteren. 

## <a name="scalable-storage"></a>Schaal bare opslag

Met Azure Container Registry kunt u zoveel opslag plaatsen, afbeeldingen, lagen of Tags maken als u nodig hebt, tot aan de [opslag limiet voor het REGI ster](container-registry-skus.md#service-tier-features-and-limits). 

Hoge aantallen opslag plaatsen en tags kunnen de prestaties van het REGI ster beïnvloeden. Verwijder regel matig ongebruikte opslag plaatsen, tags en installatie kopieën als onderdeel van de onderhouds routine voor het REGI ster en stel optioneel een [Bewaar beleid](container-registry-retention-policy.md) in voor niet-gecodeerde manifesten. Verwijderde register resources zoals opslag plaatsen, afbeeldingen en tags *kunnen niet* worden hersteld na verwijdering. Zie [container installatie kopieën verwijderen in azure container Registry](container-registry-delete.md)voor meer informatie over het verwijderen van register resources.

## <a name="storage-cost"></a>Opslagkosten

Zie [Azure container Registry prijzen][pricing]voor volledige informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure container Registry service lagen](container-registry-skus.md)voor meer informatie over de basis-, standaard-en Premium-container registers.

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
