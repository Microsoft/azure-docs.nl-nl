---
title: Continue video-opname-Azure
description: Continue video-opname (CVR) verwijst naar het proces van het continu vastleggen van de video van een video bron. In dit onderwerp wordt beschreven wat CVR is.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: f8b25d74db279f8cfff68f08efeab2975484a0c5
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453747"
---
# <a name="continuous-video-recording"></a>Continue video-opname  

## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen  

* [Mediagrafiekconcepten](media-graph-concept.md)
* [Concept voor het opnemen van video](video-recording-concept.md)

## <a name="overview"></a>Overzicht

Continue video-opname (CVR) verwijst naar het proces van het continu vastleggen van de video van een video bron. Live video Analytics op IoT Edge ondersteunt het opnemen van video continu, 24 uur per dag, vanaf een CCTV-camera via een [Media grafiek](media-graph-concept.md) die bestaat uit een RTSP-bron knooppunt en een Asset Sink-knoop punt. In het onderstaande diagram ziet u een grafische weer gave van een dergelijk media diagram. De JSON-weer gave van de [grafiek topologie](media-graph-concept.md#media-graph-topologies-and-instances) van een dergelijke media grafiek vindt u [hier](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Continue video-opname":::

De bovenstaande media grafiek kan worden uitgevoerd op een edge-apparaat, met de Asset Sink video opnemen in een Azure Media Services- [Asset](terminology.md#asset). De video wordt geregistreerd zolang de media grafiek de status geactiveerd heeft. Omdat video wordt vastgelegd als een Asset, kan deze worden afgespeeld met de bestaande streaming-mogelijkheden van Media Services. Zie [afspelen van opgenomen inhoud](video-playback-concept.md) voor meer informatie.

## <a name="resilient-recording"></a>Flexibele opname

Live video Analytics op IoT Edge ondersteunt het gebruik van minder dan perfecte netwerk omstandigheden, waarbij het edge-apparaat af en toe verbinding met de cloud kan verliezen of een daling in de beschik bare band breedte kan ondervinden. Als u dit wilt doen, wordt de video van de bron lokaal geregistreerd in een cache en wordt deze regel matig automatisch gesynchroniseerd met de Asset. Als u de JSON van de [grafiek topologie](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json)bekijkt, ziet u dat de volgende eigenschappen zijn gedefinieerd:

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
```

De laatste twee eigenschappen zijn relevant voor een flexibele opname (beide zijn ook de vereiste eigenschappen voor een Asset Sink-knoop punt). De eigenschap localMediaCachePath geeft aan dat de Asset-Sink dat mappad gebruikt om media gegevens in de cache op te slaan voordat ze naar de Asset worden geüpload. U kunt [Dit](../../iot-edge/how-to-access-host-storage-from-module.md) artikel raadplegen om te begrijpen hoe de Edge-module gebruik kan maken van de lokale opslag van uw apparaat. De eigenschap localMediaCacheMaximumSizeMiB definieert hoeveel schijf ruimte de Asset-Sink als cache kan gebruiken (1 MiB = 1024 * 1024 bytes). 

Als uw Edge-module de connectiviteit gedurende een lange periode verliest en de inhoud die in de cachemap is opgeslagen, de localMediaCacheMaximumSizeMiB-waarde bereikt, worden de gegevens uit de cache verwijderd, beginnend bij de oudste gegevens. Als het apparaat bijvoorbeeld verbinding heeft met 10AM en de cache de maximum limiet bereikt bij 18:00 uur, begint de Asset Sink met het verwijderen van gegevens die zijn geregistreerd bij 10AM. 

Wanneer de netwerk verbinding wordt hersteld, begint de Asset-Sink vanuit de cache te uploaden, vanaf de oudste gegevens. In het bovenstaande voor beeld is 5 minuten video verwijderd uit het cache geheugen door de tijd dat de verbinding is hersteld (bijvoorbeeld 6:02PM). vervolgens wordt de Asset Sink geüpload vanaf de 10:05AM-markering.

Als u het activum later bekijkt met behulp van [deze](playback-recordings-how-to.md) api's, ziet u dat er sprake is van een onderbreking in het activum van ongeveer 10AM naar 10:05AM.

## <a name="segmented-recording"></a>Gesegmenteerde opname  

Zoals hierboven beschreven, neemt het knoop punt Asset Sink video op naar een lokale cache en uploadt de video regel matig naar de Cloud. De eigenschap segmentLength (weer gegeven in de bovenstaande sectie) helpt u bij het beheren van de kosten voor schrijf transacties die zijn gekoppeld aan het schrijven van gegevens naar uw opslag account waar de Asset wordt geregistreerd. Als u bijvoorbeeld de upload periode van 30 seconden naar 5 minuten verhoogt, wordt het aantal opslag transacties met een factor van 10 (5 * 60/30) verwijderd.

De eigenschap segmentLength zorgt ervoor dat de Edge-module Maxi maal eenmaal per segmentLength seconden video uploadt. Deze eigenschap heeft een minimum waarde van 30 seconden (ook de standaard instelling) en kan worden verhoogd met een interval van 30 seconden tot een maximum van vijf minuten.

> [!NOTE]
> Zie het artikel over het [afspelen van opnamen](playback-recordings-how-to.md) voor het effect dat segmentLength op het afspelen heeft.

## <a name="see-also"></a>Zie ook

* [Video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md)
* [Afspelen van opgenomen inhoud](video-playback-concept.md)

## <a name="next-steps"></a>Volgende stappen

[Zelf studie: doorlopende video-opname](continuous-video-recording-tutorial.md)
