---
Titel: Live transcriptie: beschrijving van Azure Media Services: meer informatie over Azure Media Services Live transcriptie.  
Services: Media Services documentationcenter: ' ' Auteur: IngridAtMicrosoft Manager: femila-editor: ' MS. service: Media-Services MS. workload: media ms.tgt_pltfrm: na MS. devlang: ne MS. onderwerp: How-to MS. date: 08/31/2020 MS. Author: inhenkel

---

# <a name="live-transcription-preview"></a>Live transcriptie (preview-versie)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

De Azure media-service levert video, audio en tekst in verschillende protocollen. Wanneer u uw Live Stream publiceert met MPEG-DASH of HLS/CMAF en vervolgens video en audio, levert onze service de getranscribeerde tekst in IMSC 1.1 compatibele TTML. De levering is verpakt in fragmenten van MPEG-4 Part 30 (ISO/IEC 14496-30). Als u levering via HLS/TS gebruikt, wordt de tekst geleverd als gesegmenteerde VTT.

Er worden extra kosten in rekening gebracht wanneer Live transcriptie is ingeschakeld. Bekijk de prijs informatie in het gedeelte live video van de [pagina met Media Services prijzen](https://azure.microsoft.com/pricing/details/media-services/).

In dit artikel wordt beschreven hoe u live transcriptie inschakelt bij het streamen van een live-gebeurtenis met Azure Media Services. Voordat u doorgaat, moet u ervoor zorgen dat u bekend bent met het gebruik van Media Services v3 REST-Api's (Zie [deze zelf studie](stream-files-tutorial-with-rest.md) voor meer informatie). U moet ook vertrouwd zijn met het concept van [live streamen](live-streaming-overview.md) . Het is raadzaam om de [stroom Live met Media Services](stream-live-tutorial-with-api.md) zelf studie uit te voeren.

## <a name="live-transcription-preview-regions-and-languages"></a>Regio's en talen voor de preview-versie van Live transcriptie

Live transcriptie is beschikbaar in de volgende regio's:

- Azië - zuidoost
- Europa -west
- Europa - noord
- VS - oost
- VS - centraal
- VS - zuid-centraal
- VS - west 2
- Brazilië - zuid

Dit is de lijst met beschik bare talen die kunnen worden getranscribeerd. Gebruik de taal code in de API.

| Taal | Taalcode |
| -------- | ------------- |
| Catalaans  | ca-ES |
| Deens (Denemarken) | da-DK |
| Duits (Duitsland) | de-DE |
| Engels (Australië) | en-AU |
| Engels (Canada) | en-CA |
| Engels (Verenigd Koninkrijk) | en-GB |
| Engels (India) | en-IN |
| Engels (Nieuw-Zeeland) | en-NZ |
| Engels (Verenigde Staten) | nl-NL |
| Spaans (Spanje) | es-ES |
| Spaans (Mexico) | es-MX |
| Fins (Finland) | fi-FI |
| Frans (Canada) | fr-CA |
| Frans (Frankrijk) | fr-FR |
| Italiaans (Italië) | it-IT |
| Nederlands (Nederland) | nl-NL |
| Portugees (Brazilië) | pt-BR |
| Portugees (Portugal) | pt-PT |
| Zweeds (Zweden) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>De live-gebeurtenis met Live-transcriptie maken

Als u een live gebeurtenis wilt maken waarbij de transcriptie is ingeschakeld, verzendt u de PUT-bewerking met de API-versie 2019-05-01-preview, bijvoorbeeld:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

De bewerking heeft de volgende hoofd tekst (waarbij een Pass-through live-gebeurtenis wordt gemaakt met RTMP als het opname Protocol). Let op de toevoeging van een eigenschap transcripties.

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>Transcriptie starten of stoppen nadat de live-gebeurtenis is gestart

U kunt live transcriptie starten en stoppen terwijl de live-gebeurtenis actief is. Meer informatie over het starten en stoppen van live events vindt u in de sectie langlopende bewerkingen bij het [ontwikkelen met Media Services v3-api's](media-services-apis-overview.md#long-running-operations).

Als u live transcripties wilt inschakelen of de transcriptie wilt bijwerken, patcht u de live-gebeurtenis om een eigenschap transcripties toe te voegen. Als u live transcripties wilt uitschakelen, verwijdert u de eigenschap ' transcripties ' uit het Live Event-object.  

> [!NOTE]
> Het is niet mogelijk om de transcriptie in **of uit te scha kelen tijdens** de live-gebeurtenis.

Dit is de voor beeld-oproep om live-transcripties in te scha kelen.

VERZENDEN ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>Transcriptiee levering en afspelen

Raadpleeg het [overzicht van dynamische](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) pakketten voor informatie over hoe de service dynamische pakketten gebruikt voor het leveren van video, audio en tekst in verschillende protocollen. Wanneer u uw Live Stream publiceert met MPEG-DASH of HLS/CMAF en vervolgens video en audio, levert onze service de getranscribeerde tekst in IMSC 1.1 compatibele TTML. Deze levering is verpakt in fragmenten van MPEG-4 Part 30 (ISO/IEC 14496-30). Als u levering via HLS/TS gebruikt, wordt de tekst geleverd als gesegmenteerde VTT. U kunt een webspeler gebruiken zoals de [Azure Media Player](use-azure-media-player.md) om de stream af te spelen.  

> [!NOTE]
> Als u Azure Media Player gebruikt, moet u versie 2.3.3 of hoger gebruiken.

## <a name="known-issues"></a>Bekende problemen

Voor de preview-versie zijn de volgende bekende problemen met Live-transcriptie:

- Apps moeten de preview-Api's gebruiken, zoals beschreven in de [Media Services v3 OpenAPI-specificatie](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
- DRM-beveiliging (Digital Rights Management) is niet van toepassing op de tekst track, alleen AES-envelop versleuteling is mogelijk.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Media Services](media-services-overview.md)
