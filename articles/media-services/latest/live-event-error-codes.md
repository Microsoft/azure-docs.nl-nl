---
title: Fout codes voor Live-gebeurtenissen Azure Media Services
description: In dit artikel vindt u een overzicht van fout codes voor Live-gebeurtenissen.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 310e70bc8c7ddbf3bcd37d4f0276fe04c2979e73
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897846"
---
# <a name="media-services-live-event-error-codes"></a>Fout codes voor Live-gebeurtenissen Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In de tabellen in deze sectie worden de fout codes voor [Live-gebeurtenissen](live-events-outputs-concept.md) vermeld.

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Wanneer u zich abonneert op de [Event grid](../../event-grid/index.yml) gebeurtenissen voor een live gebeurtenis, ziet u mogelijk een van de volgende fouten van de [LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected)  -gebeurtenis.

| Resultaatcode | Beschrijving |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | Onjuiste opname-URL |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | IP encoder is niet aanwezig in de lijst met toegestane IP-adressen |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | Het RTMP-coderings programma heeft geen setDataFrame-opdracht verzonden. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | De opgegeven codec wordt niet ondersteund. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |De informatie over de media beschrijving is niet ontvangen voordat de daad werkelijke media gegevens zijn geleverd.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |Het aantal kwaliteiten voor het audio-of video type overschrijdt de Maxi maal toegestane limiet.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |De totale binnenkomende bitrate in een live event of kanaal service heeft de toegestane maximum limiet overschreden.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | De tijds tempel van de video-of audio-FLVTag is ongeldig vanuit het RTMP-coderings programma. |
| MPE_INGEST_FRAMERATE_EXCEEDED | De binnenkomende code ring opgenomen streams met frame snelheid overschrijdt het Maxi maal toegestane aantal 30fps voor het coderen van Live-gebeurtenissen/kanalen.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | De binnenkomende gegevensstromen die zijn opgenomen streams overschrijden de volgende toegestane oplossingen: 1920x1088 voor het coderen van Live Events/kanalen en 4096 x 2160 voor Pass-Through Live-gebeurtenissen/kanalen.|
| MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV | De live-gebeurtenis heeft een grote hoeveelheid audio gegevens tegelijk ontvangen, of een grote hoeveelheid video gegevens zonder keyframes. De verbinding met het coderings programma is verbroken, zodat het een nieuwe poging is met de juiste gegevens. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Mogelijk wordt een van de volgende fouten weer geven in de [LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) -gebeurtenis.

|Resultaatcode|Beschrijving|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|Er is een time-out opgetreden voor RTMP-sessie na inactiviteit van de toegestane tijds limiet.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|De tijds tempel van de video-of audio-FLVTag is ongeldig vanuit het RTMP-coderings programma.|
|MPE_CAPACITY_LIMIT_REACHED|Coderings programma verzendt gegevens te snel.|
|Onbekende fout codes|Deze fout codes kunnen variëren van geheugen fout om vermeldingen in de hash-map te dupliceren.|


## <a name="see-also"></a>Zie ook

[Fout codes voor streaming-eind punten (oorsprong)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: Live streamen met Media Services](stream-live-tutorial-with-api.md)
