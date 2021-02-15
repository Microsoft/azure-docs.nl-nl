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
ms.date: 02/12/2020
ms.author: inhenkel
ms.openlocfilehash: b3be465c488bdd3c5dbd62f757733939d1bee393
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393510"
---
# <a name="media-services-live-event-error-codes"></a>Fout codes voor Live-gebeurtenissen Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In de volgende tabellen worden de fout codes voor [Live-gebeurtenissen](live-events-outputs-concept.md) weer geven.

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Wanneer u zich abonneert op de [Event grid](../../event-grid/index.yml) gebeurtenissen voor een live gebeurtenis, ziet u mogelijk een van de volgende fouten van de [LiveEventConnectionRejected](media-services-event-schemas.md\#liveeventconnectionrejected) -gebeurtenis.
> [!div class="mx-tdCol2BreakAll"]
>| Fout | Informatie |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|Description | Onjuiste opname-URL |
>|Voorgestelde oplossing| APPID is een GUID-token in RTMP-opname-URL. Zorg ervoor dat deze overeenkomt met de opname-URL van de API. |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| Description |Encoder-IP is niet aanwezig in de geconfigureerde lijst met toegestane IP-adressen |
>| Voorgestelde oplossing| Controleer of het IP-adres van het coderings programma in de lijst met toegestane IP-adressen voor komt. Gebruik een online hulp programma zoals *whoismyip* of *CIDR Calculator* om de juiste waarde in te stellen.  Zorg ervoor dat het coderings programma de server kan bereiken vóór de werkelijke live-gebeurtenis. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| Description|Het RTMP-coderings programma heeft de opdracht niet verzonden `setDataFrame` . |
>| Voorgestelde oplossing|De meeste commerciële encoders verzenden meta gegevens van streams. Voor een coderings programma dat een enkele bitrate-opname pusht, is dit mogelijk niet het probleem. De LiveEvent kan binnenkomende bitrate berekenen wanneer de meta gegevens van de stroom ontbreken.  Voor het opnemen van multi-bitrate voor een PassThru-kanaal of een dubbel push-scenario kunt u proberen de query reeks toe te voegen met ' videodatarate ' en ' audiodatarate ' in de opname-URL. De geschatte waarde kan werken. De eenheid bevindt zich in kbit. Bijvoorbeeld:  `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| Description|De opgegeven codec wordt niet ondersteund.|
>| Voorgestelde oplossing| De LiveEvent heeft een niet-ondersteunde codec ontvangen. Bijvoorbeeld, een RTMP-opname, LiveEvent heeft een niet-AVC-videocodec ontvangen.  Controleer de encoder-voor instelling. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| Description |De informatie over de media beschrijving is niet ontvangen voordat de daad werkelijke media gegevens zijn geleverd. |
>| Voorgestelde oplossing|De LiveEvent ontvangt geen beschrijving van de stroom (header-of FLV-tag) van het coderings programma. Dit is een schending van het protocol. Neem contact op met de leverancier van encoder. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| Description|Het aantal kwaliteiten voor het audio-of video type overschrijdt de Maxi maal toegestane limiet. |
>| Voorgestelde oplossing|Wanneer Live Event mode is Live Encoding, moet het coderings programma een enkele bitrate van video en audio pushen.  Houd er rekening mee dat een redundante Push van dezelfde bitrate is toegestaan. Controleer de vooraf ingestelde code ring of de uitvoer instellingen om er zeker van te zijn dat er één bitrate stroom wordt uitgevoerd. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| Description|De totale binnenkomende bitrate in een live event of kanaal service heeft de toegestane maximum limiet overschreden. |
>| Voorgestelde oplossing|Het coderings programma heeft de maximale binnenkomende bitrate overschreden. Deze limiet aggregeert alle inkomende gegevens van de bijdragende encoder. Controleer de vooraf ingestelde code ring of de uitvoer instellingen om de bitsnelheid te verminderen. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| Description|De tijds tempel van de video-of audio-FLVTag is ongeldig vanuit het RTMP-coderings programma. |
>| Voorgestelde oplossing|Afgeschaft. |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| Description|De binnenkomende code ring opgenomen streams met frame snelheid overschrijdt het Maxi maal toegestane aantal van 30 fps voor het coderen van Live gebeurtenissen/kanalen. |
>| Voorgestelde oplossing|Controleer de code ring vooraf om de frame frequentie onder 36 fps te verlagen. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| Description|De binnenkomende gegevensstromen die zijn opgenomen streams overschrijden de volgende toegestane oplossingen: 1920x1088 voor het coderen van Live Events/kanalen en 4096 x 2160 voor Pass-Through Live-gebeurtenissen/kanalen. |
>| Voorgestelde oplossing|Controleer de encoder-voor instelling om de video resolutie te verlagen, zodat deze de limiet niet overschrijdt. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| Description|De live-gebeurtenis heeft een grote hoeveelheid audio gegevens tegelijk ontvangen, of een grote hoeveelheid video gegevens zonder keyframes. De verbinding met het coderings programma is verbroken, zodat het een nieuwe poging is met de juiste gegevens. |
>| Voorgestelde oplossing|Zorg ervoor dat het coderings programma een sleutel frame verzendt voor elke sleutel frame interval (GOP terug).  Schakel instellingen als ' constante bitrate ' of ' keyframes uitlijnen ' in. Soms kan het nodig zijn om de bijdragende Encoder opnieuw in te stellen. Als het niet helpt, neemt u contact op met de leverancier van encoder. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Mogelijk wordt een van de volgende fouten weer geven in de [LiveEventEncoderDisconnected](media-services-event-schemas.md\#liveeventencoderdisconnected) -gebeurtenis.

> [!div class="mx-tdCol2BreakAll"]
>| Fout | Informatie |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| Description|Er is een time-out opgetreden voor RTMP-sessie na inactiviteit van de toegestane tijds limiet. |
>|Voorgestelde oplossing|Dit gebeurt meestal wanneer een coderings programma de invoer niet meer ontvangt, zodat de sessie niet-actief wordt omdat er geen gegevens zijn om te pushen. Controleer of de status van het coderings programma of de invoer feed een goede status heeft. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|Description| De tijds tempel van de video-of audio-FLVTag is ongeldig vanuit het RTMP-coderings programma. |
>| Voorgestelde oplossing| Afgeschaft. |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| Description|Coderings programma verzendt gegevens te snel. |
>| Voorgestelde oplossing|Dit gebeurt wanneer het coderings programma een grote set fragmenten in een korte periode opsplitst.  Dit kan theoretisch gebeuren wanneer het coderings programma geen gegevens kan pushen tijdens een netwerk probleem en de gegevens uitbreek wanneer het netwerk beschikbaar is. Zoek de reden in het logboek van de encoder of het systeem logboek. |
>|**Onbekende fout codes** |
>| Description| Deze fout codes kunnen variëren van geheugen fout om vermeldingen in de hash-map te dupliceren. |

## <a name="other-error-codes"></a>Overige foutcodes

> [!div class="mx-tdCol2BreakAll"]
>| Fout | Informatie |Geweigerde/verbroken gebeurtenis|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||Yes|
>| Description|Dit is een algemene fout. ||
>|Voorgestelde oplossing| Geen.||
>|**MPI_SYSTEM_MAINTENANCE** ||Yes|
>| Description|De verbinding met het coderings programma is verbroken vanwege een service-update of systeem onderhoud. ||
>|Voorgestelde oplossing|Zorg ervoor dat het coderings programma automatische verbinding maken is ingeschakeld. Dit is een coderings functie voor het herstellen van de onverwachte sessie verbreken. ||
>|**MPE_BAD_URL_SYNTAX** ||Yes|
>| Description|De opname-URL heeft een onjuiste indeling. ||
>|Voorgestelde oplossing|Zorg ervoor dat de opname-URL correct is ingedeeld. Voor RTMP moet het `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||Yes|
>| Description|Het coderings programma heeft de sessie verbroken.  ||
>|Voorgestelde oplossing|Dit is niet het probleem. Dit is het geval waarbij het coderings programma de verbinding heeft verbroken, inclusief een probleemloze verbreken van de verbinding. Als dit een onverwacht verbreking van de verbinding is, controleert u het coderings logboek of het systeem logboek. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||Nee|
>| Beschrijving|De binnenkomende gegevens verhouding komt niet overeen met de verwachte bitrate. ||
>|Voorgestelde oplossing|Dit is een waarschuwing die zich voordoet wanneer de frequentie van binnenkomende gegevens te langzaam of snel is. Controleer het coderings logboek of het systeem logboek.||
>|**MPE_INGEST_DISCONTINUITY** ||Nee|
>| Beschrijving| Er bevindt zich discontinuty in binnenkomende gegevens.||
>|Voorgestelde oplossing| Dit is een waarschuwing dat het coderings programma gegevens verbreekt vanwege een netwerk probleem of een probleem met de systeem bronnen. Controleer het encoder logboek of het systeem logboek. Bewaak ook de systeem bron (CPU, geheugen of netwerk). Als de CPU van het systeem te hoog is, probeert u de bitsnelheid te verlagen of gebruikt u de optie voor H/W-code ring van de grafische kaart van het systeem.||

## <a name="see-also"></a>Zie ook

[Fout codes voor streaming-eind punten (oorsprong)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: Live streamen met Media Services](stream-live-tutorial-with-api.md)
