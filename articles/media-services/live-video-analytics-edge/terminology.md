---
title: Live video Analytics op IoT EDG-terminologie-Azure
description: Dit artikel bevat een overzicht van live video analyses op IoT Edge terminologie.
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: e3a77b69adf2241a4af2652db4edb6673a63b4f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88690609"
---
# <a name="terminology"></a>Terminologie

Dit artikel bevat een overzicht van de terminologie die betrekking heeft op [Live video Analytics op IOT Edge](overview.md).

## <a name="azure-media-services"></a>Azure Media Services

Azure Media Services is een platform voor Cloud media waarmee u media oplossingen kunt bouwen. Meer informatie hierover vindt u in de [Azure Media Services](../latest/media-services-overview.md) documentatie.

## <a name="asset"></a>Asset

[Asset](../latest/assets-concept.md) is een entiteit in azure Media Services die wordt toegewezen aan een BLOB-container in het Azure Storage-account dat is gekoppeld aan een Media Services-account. Alle bestanden die zijn gekoppeld aan een Asset, worden opgeslagen als blobs in die container terwijl Media Services de meta gegevens (bijvoorbeeld een naam, beschrijving, aanmaak tijd) bevat die aan de Asset zijn gekoppeld.

Live video Analytics op IoT Edge kan assets maken en/of gegevens toevoegen aan bestaande assets. Hierdoor kunnen de scenario's van doorlopend en op gebeurtenissen gebaseerde video-opname en-afspelen (met video-opname op het apparaat aan de rand, het vastleggen van Azure Media Services en het afspelen via bestaande Azure Media Services streaming-mogelijkheden) worden uitgevoerd.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) is een taal-neutraal, het RPC-Framework (Remote Procedure Call) met hoge prestaties. Het maakt gebruik van op sessies gebaseerde gestructureerde schema's via [protocol buffers 3](https://developers.google.com/protocol-buffers/docs/proto3) als de onderliggende indeling voor bericht uitwisseling voor communicatie.

## <a name="media-graph"></a>Mediagrafiek

Met [Media Graph](media-graph-concept.md) kunt u bepalen waar media moeten worden vastgelegd, hoe deze moeten worden verwerkt en waar de resultaten moeten worden bezorgd. Hiermee kunt u een grafiek definiëren die bestaat uit bronnen, processors en Sink-knoop punten. Daarom biedt u de mogelijkheid om live video Analytics-toepassingen te bouwen. Media Graph wordt gedetailleerd beschreven op de concept pagina van media Graph.

## <a name="recording"></a>Opnemen

In de context van een video beheersysteem voor beveiligings camera's verwijst de video-opname naar het proces van het vastleggen van video van de camera's en het opslaan in een bestand (of bestanden) voor de volgende weer gave via mobiele en browser-apps. Video-opname kan worden gecategoriseerd in [doorlopende video-opnamen](continuous-video-recording-concept.md) en [op gebeurtenissen gebaseerde video-opnamen](event-based-video-recording-concept.md). Deze worden gedetailleerd beschreven in de concept pagina voor het [opnemen van video](video-recording-concept.md) .

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) verwijst naar Real-Time streaming-protocol. Het is een protocol op toepassings niveau voor controle over de levering van gegevens met realtime-eigenschappen. RTSP biedt een uitbreidbaar Framework voor het inschakelen van beheerde, on-demand levering van real-time gegevens, zoals audio en video. 

## <a name="streaming"></a>Streaming

Als u video op een mobiel apparaat hebt bekeken uit services zoals Netflix, YouTube en anderen, hebt u ervaring met het streamen van video. Het afspelen begint binnenkort nadat u ' afspelen ' hebt bereikt (als u voldoende band breedte hebt) en u kunt teruggaan naar de tijd lijn van de video. Met streaming is het verstandig om alleen het gedeelte van de weer te geven video te leveren en de viewer de video te laten afspelen terwijl de gegevens nog steeds van een server naar de Play-client worden overgebracht. In de context van Azure Media Services verwijst [streaming](https://en.wikipedia.org/wiki/Streaming_media) naar het proces van het leveren van Media van [Azure Media Services](../azure-media-player/azure-media-player-overview.md) aan een streaming-client (bijvoorbeeld Azure Media Player). U kunt Azure Media Services gebruiken om video naar clients te streamen met behulp van industrie standaard, op HTTP gebaseerde protocollen voor mediastreaming zoals [http live streaming (HLS)](https://developer.apple.com/streaming/) en [MPEG-Dash](https://dashif.org/about/). HLS wordt ondersteund door Azure Media Player en webspelers zoals [JW Player](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [VideoJS](https://videojs.com/), [de Shake-speler van Google](https://github.com/google/shaka-player), of u kunt systeem eigen in mobiele apps weer geven met behulp van de [Exoplayer](https://github.com/google/ExoPlayer) van Android en de [AV Foundation](https://developer.apple.com/av-foundation/)van Ios. MPEG-DASH wordt ook ondersteund door Azure Media Player, [een lijst met clients op deze pagina zoeken](https://dashif.org/clients/). 

Door [Media Graph](#media-graph)s te gebruiken om Video's op te nemen in een asset in azure Media Services, kunt u Media Services streaming-mogelijkheid gebruiken om video-streams in HLS en Dash te leveren. Meer informatie hierover vindt u in het artikel [video afspelen](video-playback-concept.md) .

## <a name="vms"></a>VIRTUELE

[Vm's](https://en.wikipedia.org/wiki/Video_management_system) verwijzen naar een video beheersysteem. Dergelijke systemen worden gebruikt voor het configureren en beheren van CCTV-camera's, het vastleggen en opnemen van Video's. Deze systemen bieden ook client toepassingen voor het afspelen van de opgenomen video

## <a name="next-steps"></a>Volgende stappen

[Media grafieken](media-graph-concept.md)
