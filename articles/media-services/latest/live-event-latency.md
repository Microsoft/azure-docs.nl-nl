---
title: Instellingen voor LiveEvent lage latentie in Azure Media Services
description: Dit onderwerp bevat een overzicht van de instellingen voor LiveEvent-lage latentie en laat zien hoe u een lage latentie kunt instellen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 490b9d54aa3b661124699a472b453f80d9c39963
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705361"
---
# <a name="live-event-low-latency-settings"></a>Instellingen voor lage latentie van Live Event

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel wordt beschreven hoe u een lage latentie kunt instellen voor een [live gebeurtenis](/rest/api/media/liveevents). Ook worden de gebruikelijke resultaten besproken die u ziet wanneer u de instellingen voor lage latentie gebruikt in verschillende spelers. De resultaten variëren op basis van CDN en netwerk latentie.

Als u de nieuwe functie **LowLatency** wilt gebruiken, stelt u de **StreamOptionsFlag** in op **LowLatency** op de **LiveEvent**. Wanneer u [LiveOutput](/rest/api/media/liveoutputs) maakt voor HLS afspelen, stelt u [LiveOutput. HLS. fragmentsPerTsSegment](/rest/api/media/liveoutputs/create#hls) in op 1. Zodra de stroom actief is, kunt u de [Azure Media Player](https://ampdemo.azureedge.net/) (pagina amp-demo) gebruiken en de afspeel opties instellen op het gebruik van het profiel ' laag latentie heuristiek '.

> [!NOTE]
> Op dit moment is de LowLatency HeuristicProfile in Azure Media Player ontworpen voor het afspelen van streams in MPEG-DASH protocol, met een KVP-of CMAF-indeling (bijvoorbeeld `format=mdp-time-csf` of `format=mdp-time-cmaf` ). 

In het volgende .NET-voor beeld ziet u hoe u **LowLatency** instelt op de **LiveEvent**:

[!code-csharp[Main](../../../media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs#NewLiveEvent)]

        

Bekijk het volledige voor beeld: [live event with DVR](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs).

## <a name="live-events-latency"></a>Latentie van Live-gebeurtenissen

In de volgende tabellen worden de gemiddelde resultaten weer gegeven voor latentie (wanneer de vlag LowLatency is ingeschakeld) in Media Services, gemeten vanaf het moment dat de bijdrage toevoer de service bereikt wanneer een viewer het afspelen in de speler ziet. Als u de maximale latentie optimaal wilt gebruiken, moet u de instellingen voor de code ring afstemmen op 1 seconde ' groep afbeeldingen ' (GOP terug). Wanneer u een hogere GOP terug lengte gebruikt, minimaliseert u het bandbreedte verbruik en vermindert u de bitsnelheid onder dezelfde frame frequentie. Het is vooral nuttig in Video's met minder beweging.

### <a name="pass-through"></a>Pass-through 

||2-GOP terug lage latentie ingeschakeld|1S GOP terug lage latentie ingeschakeld|
|---|---|---|
|**STREEPJE in AMP**|10|8s|
|**HLS op systeem eigen iOS-speler**|14s|10|

### <a name="live-encoding"></a>Live Encoding

||2-GOP terug lage latentie ingeschakeld|1S GOP terug lage latentie ingeschakeld|
|---|---|---|
|**STREEPJE in AMP**|14s|10|
|**HLS op systeem eigen iOS-speler**|18s|13s|

> [!NOTE]
> De end-to-end-latentie kan variëren afhankelijk van de lokale netwerk omstandigheden of door een CDN-cache laag te introduceren. U moet uw exacte configuraties testen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van live streamen](live-streaming-overview.md)
- [Zelf studie over live streamen](stream-live-tutorial-with-api.md)
