---
title: Container installatie kopie opslag
description: Meer informatie over hoe uw container installatie kopieën en andere artefacten worden opgeslagen in Azure Container Registry, met inbegrip van beveiliging, redundantie en capaciteit.
ms.topic: article
ms.date: 03/03/2021
ms.custom: references_regions
ms.openlocfilehash: ec4328b44d5493b8d765fa30c548adc3d747d446
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183264"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Opslag van container installatie kopieën in Azure Container Registry

Elke [basis, standaard en Premium](container-registry-skus.md) Azure container Registry-voor delen van geavanceerde functies van Azure Storage, inclusief versleuteling-at-rest. In de volgende secties worden de functies en limieten beschreven van installatie kopie opslag in Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Versleuteling-at-rest

Alle container installatie kopieën en andere artefacten in uw REGI ster worden op rest versleuteld. Azure versleutelt automatisch een afbeelding voordat deze wordt opgeslagen en ontsleutelt deze op het moment dat u of uw toepassingen en services de installatie kopie ophalen. Pas desgewenst een extra versleutelings laag toe met een door de [klant beheerde sleutel](container-registry-customer-managed-keys.md).

## <a name="regional-storage"></a>Regionale opslag

Azure Container Registry slaat gegevens op in de regio waarin het REGI ster is gemaakt, zodat klanten kunnen voldoen aan de vereisten voor gegevens locatie en naleving.

Voor een betere beveiliging tegen data centers is het mogelijk dat bepaalde regio's [zone redundantie](zone-redundancy.md)bieden, waarbij gegevens worden gerepliceerd over meerdere data centers in een bepaalde regio.

Klanten die hun gegevens willen opslaan in meerdere regio's voor betere prestaties in verschillende geografische gebieden of die tolerantie willen hebben in het geval van een regionale storing, moeten [geo-replicatie](container-registry-geo-replication.md)inschakelen.

## <a name="geo-replication"></a>Geo-replicatie

Voor scenario's waarvoor hoge Beschik baarheid is vereist, kunt u overwegen de [geo-replicatie](container-registry-geo-replication.md) functie van Premium-registers te gebruiken. Geo-replicatie biedt beveiliging tegen verlies van toegang tot uw REGI ster in het geval van een regionale storing. Geo-replicatie biedt ook andere voor delen, zoals de opslag van installatie kopieën van het netwerk, voor snellere pushes en pull-bewerkingen in gedistribueerde ontwikkel-of implementatie scenario's.

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
