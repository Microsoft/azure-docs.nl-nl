---
title: Video-opname op basis van gebeurtenissen-Azure
description: Video-opname op basis van gebeurtenissen (EVR) verwijst naar het proces van het opnemen van video geactiveerd door een gebeurtenis. De betrokken gebeurtenis kan afkomstig zijn als gevolg van het verwerken van het video signaal zelf (bijvoorbeeld detectie op beweging) of kan afkomstig zijn uit een onafhankelijke bron (bijvoorbeeld het openen van een deur).  In dit artikel worden enkele gebruiks voorbeelden besproken die betrekking hebben op op gebeurtenissen gebaseerde video-opname.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 6a5f4873b2cfef8d9a6594916d82cd30a3bc1cc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97401595"
---
# <a name="event-based-video-recording"></a>Video-opname op basis van gebeurtenissen  
 
## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen  

* [Continue video-opname](continuous-video-recording-concept.md)
* [Afspelen van opgenomen inhoud](video-playback-concept.md)
* [Mediagrafiekconcepten](media-graph-concept.md)

## <a name="overview"></a>Overzicht 

Video-opname op basis van gebeurtenissen (EVR) verwijst naar het proces van het opnemen van video geactiveerd door een gebeurtenis. De betrokken gebeurtenis kan afkomstig zijn als gevolg van het verwerken van het video signaal zelf (bijvoorbeeld detectie op beweging) of kan afkomstig zijn uit een onafhankelijke bron (bijvoorbeeld het openen van een deur). 

U kunt video (geactiveerd door een gebeurtenis) opnemen van een CCTV-camera naar een Media Services-Asset met behulp van een media grafiek die bestaat uit een [RTSP-bron](media-graph-concept.md#rtsp-source) knooppunt, een [Asset Sink](media-graph-concept.md#asset-sink) -knoop punt en andere knoop punten, zoals beschreven in de onderstaande gebruiks voorbeelden. Telkens wanneer er een gebeurtenis optreedt, kunt u het knoop punt [Asset Sink](media-graph-concept.md#asset-sink) zo configureren dat er nieuwe assets worden gegenereerd, zodat de video die overeenkomt met elke gebeurtenis in zijn eigen activa wordt weer gegeven. U kunt er ook voor kiezen om één Asset te gebruiken voor het opslaan van de video voor alle gebeurtenissen. 

Als alternatief voor het knoop punt Asset sink kunt u een bestand- [sink](media-graph-concept.md#file-sink) -knoop punt gebruiken voor het vastleggen van korte fragmenten van video (gerelateerd aan een belang rijke gebeurtenis), naar een opgegeven locatie op het lokale bestands systeem op het apparaat van de Edge. 

In dit artikel worden enkele gebruiks voorbeelden besproken die betrekking hebben op op gebeurtenissen gebaseerde video-opname.

### <a name="video-recording-based-on-motion-detection"></a>Video-opname op basis van bewegings detectie  

In dit geval kunt u video clips alleen opnemen wanneer er beweging is gedetecteerd in de video en worden gewaarschuwd wanneer er een video clip wordt gegenereerd. Dit kan relevant zijn voor commerciële beveiligings scenario's met betrekking tot de beveiliging van een kritieke infra structuur. Door waarschuwingen te genereren en video op te nemen wanneer onverwachte bewegingen worden gedetecteerd, kunt u de efficiëntie van de menselijke operator verbeteren omdat actie alleen nodig is wanneer de waarschuwing wordt gegenereerd.

In het onderstaande diagram ziet u een grafische weer gave van een media grafiek die deze use-case behandelt. De JSON-weer gave van de grafiek topologie van een dergelijke media grafiek vindt u [hier](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="Video-opname op basis van bewegings detectie":::

In het diagram legt het RTSP-bron knooppunt de live video-feed vast van de camera en levert deze aan een knoop punt voor [bewegings detectie processor](media-graph-concept.md#motion-detection-processor) . Als er bewegingen worden gedetecteerd in de live video, genereert het knoop punt voor de bewegings detectie een gebeurtenis die naar het knoop punt van de [Signal Gate-processor](media-graph-concept.md#signal-gate-processor) gaat en naar het knoop punt IOT hub Message sink. Het laatste knoop punt verzendt de gebeurtenissen naar de IoT Edge hub, van waar ze kunnen worden doorgestuurd naar andere doelen om waarschuwingen te activeren. 

Met een gebeurtenis van het knoop punt bewegings detectie wordt het knoop punt van de signaal Gate-processor geactiveerd en wordt de live video-feed van het RTSP-bron knooppunt door gegeven aan het knoop punt Asset sink. Als daaropvolgende bewegings detectie gebeurtenissen ontbreken, wordt de poort na een geconfigureerde tijds duur gesloten. Hiermee bepaalt u de duur van de opgenomen video.

### <a name="video-recording-based-on-events-from-other-sources"></a>Video-opname op basis van gebeurtenissen uit andere bronnen  

In dit geval kunnen signalen van een andere IoT-sensor worden gebruikt voor het activeren van de video-opname. In het onderstaande diagram ziet u een grafische weer gave van een media grafiek die deze use-case behandelt. De JSON-weer gave van de grafiek topologie van een dergelijke media grafiek vindt u [hier](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="Video-opname op basis van gebeurtenissen uit andere bronnen":::

In het diagram verzendt de externe sensor gebeurtenissen naar de IoT Edge hub. De gebeurtenissen worden vervolgens doorgestuurd naar het knoop punt van de Signal Gate-processor via het bron knooppunt van het [IOT hub bericht](media-graph-concept.md#iot-hub-message-source) . Het gedrag van het knoop punt van de signaal poort processor is hetzelfde als bij de vorige use-case: de live video-feed wordt geopend vanaf het RTSP-bron knooppunt naar het knoop punt voor bestands Sinks (of het knoop punt Asset Sink) wanneer het wordt geactiveerd door de externe gebeurtenis. 

Als u een bestand Sink-knoop punt gebruikt, wordt de video vastgelegd op het lokale bestands systeem op het apparaat van de rand. Als u een Asset Sink-knoop punt gebruikt, wordt de video vastgelegd in een Asset.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>Video-opname op basis van een externe module voor het afnemen van interferentie 

In dit geval kunt u video clips opnemen op basis van een signaal van een extern logica systeem. Een voor beeld van een dergelijke use-case is dat alleen een video clip kan worden opgenomen wanneer er een truck wordt gedetecteerd in de video feed van verkeer op een weg. In het onderstaande diagram ziet u een grafische weer gave van een media grafiek die deze use-case behandelt. De JSON-weer gave van de grafiek topologie van een dergelijke media grafiek vindt u [hier](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="Video-opname op basis van een externe module voor het afnemen van interferentie":::

In het diagram legt het RTSP-bron knooppunt de live video-feed van de camera vast en levert deze aan twee vertakkingen: er is een [signaal poort-processor](media-graph-concept.md#signal-gate-processor) knooppunt en de andere gebruikt een [http-extensie](media-graph-concept.md) knooppunt voor het verzenden van gegevens naar een externe Logic-module. Met het knoop punt HTTP-extensie kan de media grafiek afbeeldings frames (in JPEG-, BMP-of PNG-indeling) verzenden naar een externe service voor het afwijzen van de REST. Dit signalerings traject kan doorgaans alleen ondersteuning bieden voor lage frame snelheden (<5fps). U kunt het knoop punt HTTP-extensie processor gebruiken om de frame frequentie van de video te verlagen die naar de module voor externe afleiding wordt verzonden.

De resultaten van de externe service voor afwijzen worden opgehaald door het knoop punt HTTP-extensie en door gegeven aan de IoT Edge hub via IoT Hub Message Sink-knoop punt, waar ze verder kunnen worden verwerkt door de module Externe logica. Als de service voor het afleiden van Voer tuigen kan worden gedetecteerd, kan de Logic-module bijvoorbeeld een specifiek medium, zoals een bus of een truck, zoeken. Wanneer de Logic-module het object van belang detecteert, kan het het knoop punt van de signaal Gate-processor activeren door via de IoT Edge hub een gebeurtenis te verzenden naar het bron knooppunt IoT Hub bericht in de grafiek. De uitvoer van de signaal Gate wordt weer gegeven om te gaan naar een bestand Sink-knoop punt of een Asset Sink-knoop punt. In het eerste geval wordt de video vastgelegd op het lokale bestands systeem op het apparaat van de rand. In het laatste geval wordt de video opgenomen in een Asset.

Een uitbrei ding van dit voor beeld is het gebruik van een bewegings detector-processor vóór het knoop punt van de HTTP-extensie processor. Dit vermindert de belasting van de afnemende service, zoals Nighttime wanneer er lange Peri Oden zijn wanneer er geen voer tuigen op de weg zijn. 

## <a name="next-steps"></a>Volgende stappen

[Zelf studie: video-opname op basis van gebeurtenissen](event-based-video-recording-tutorial.md)
