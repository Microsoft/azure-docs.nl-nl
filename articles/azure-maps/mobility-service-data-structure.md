---
title: Gegevens structuren van Mobility Services (preview-versie) in Microsoft Azure Maps
description: Krijg inzicht in de manier waarop gegevens worden ingedeeld in metro gebieden in Azure Maps Mobility-Services (preview). Bekijk in welke velden informatie over open bare doorvoer onderbrekingen en-lijnen worden opgeslagen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8ffedc18ba331733723a6293756b60b733cc32cf
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904717"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Gegevens structuren in Azure Maps Mobility-Services (preview) 

> [!IMPORTANT]
> Azure Maps Mobility Services zijn momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.



In dit artikel wordt het concept van het metro gebied in [Azure Maps Mobility-Services](/rest/api/maps/mobility)geïntroduceerd. We bespreken enkele algemene velden die worden geretourneerd wanneer deze service wordt opgevraagd voor open bare doorvoer onderbrekingen en-lijnen. We raden u aan dit artikel te lezen voordat u de Mobility Services-Api's ontwikkelt.

## <a name="metro-area"></a>Metro gebied

De gegevens van Mobility Services (preview) worden gegroepeerd op ondersteunde metro gebieden. Metro gebieden volgen niet de grenzen van steden. Een metro gebied kan meerdere steden, gevulde stad en rond steden bevatten. Een land/regio kan eigenlijk een metro gebied zijn. 

De `metroID` is de id van een metro gebied dat kan worden gebruikt voor het aanroepen van de informatie-API voor het ophalen van een [metro gebied](/rest/api/maps/mobility/getmetroareainfopreview). Gebruik Azure Maps ' "een metro-API verkrijgen" om doorvoer typen, doorvoer instanties, actieve waarschuwingen en aanvullende Details voor de gekozen metro lijn aan te vragen. U kunt ook de ondersteunde metro gebieden en metroIDs aanvragen. De Id's van een metro gebied kunnen worden gewijzigd.

**metroID:** 522-   **naam:** Seattle-Tacoma-Bellevue

![Seattle-Metro, gebied](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Stop-Id's

Er kunnen door doorvoer beëindigingen worden verwezen door twee typen Id's, de GFTS-id [(General Transit feed Specification)](http://gtfs.org/) en de Azure Maps stop-id. De GFTS-ID wordt aangeduid als de stopKey en de Azure Maps stop-ID wordt stopID genoemd. Wanneer vaak door Voer wordt geadviseerd, wordt u aangeraden de Azure Maps stop-ID te gebruiken. stopID is stabieler en waarschijnlijk hetzelfde, zolang de fysieke stop bestaat. De GTFS-stop-ID wordt vaker bijgewerkt. Bijvoorbeeld, GTFS stop-ID kan worden bijgewerkt per GTFS provider aanvraag of wanneer een nieuwe GTFS-versie wordt uitgebracht. Hoewel de fysieke stop geen wijziging heeft, kan de GTFS-stop-ID worden gewijzigd.

Als u wilt beginnen, kunt u stoppen met het verzoek om een transit op te halen met behulp van de [API](/rest/api/maps/mobility/getnearbytransitpreview)voor het

## <a name="line-groups-and-lines"></a>Lijn groepen en-regels

Mobility Services (preview) gebruiken een parallel gegevens model voor lijnen en lijn groepen. Dit model wordt gebruikt om beter te kunnen omgaan met wijzigingen die zijn overgenomen van [GTFS](http://gtfs.org/) -routes en de TRIPS-gegevens.


### <a name="line-groups"></a>Regel groepen

Een regel groep is een entiteit waarmee alle regels die logisch deel uitmaken van dezelfde groep worden gegroepeerd. Normaal gesp roken bevat een lijn groep twee regels, één van punt A tot en met B, en de andere retour van punt B naar A. Beide regels behoren tot hetzelfde open bare transport Agentschap en hebben hetzelfde regel nummer. Het kan echter voor komen dat een regel groep meer dan twee regels of één regel bevat.


### <a name="lines"></a>Lijnen

Zoals hierboven is beschreven, bestaat elke regel groep uit een set regels. Elke regel groep bestaat uit twee regels en elke regel beschrijft een richting.  Er zijn echter gevallen waarin meer regels een regel groep vormen. Er is bijvoorbeeld een regel die soms een bepaalde groep afrondt en soms niet. In beide gevallen werkt deze onder hetzelfde regel nummer. Een regel groep kan ook bestaan uit één regel. Een ronde lijn met één richting is een groep met één regel.

Als u wilt beginnen, kunt u regel groepen aanvragen met behulp van de [regel-API voor transit ophalen](/rest/api/maps/mobility/gettransitlineinfopreview).


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het aanvragen van doorvoer gegevens met behulp van Mobility Services (preview):

> [!div class="nextstepaction"]
> [Transit gegevens aanvragen](how-to-request-transit-data.md)

Meer informatie over het aanvragen van real-time gegevens met behulp van Mobility Services (preview):

> [!div class="nextstepaction"]
> [Real-time gegevens aanvragen](how-to-request-real-time-data.md)

De API-documentatie voor Azure Maps Mobility Services (preview) verkennen

> [!div class="nextstepaction"]
> [API-documentatie voor Mobility Services](/rest/api/maps/mobility)