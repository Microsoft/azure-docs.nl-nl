---
title: Video-en audio bestanden analyseren
titleSuffix: Azure Media Services
description: Meer informatie over het analyseren van audio-en video-inhoud met behulp van AudioAnalyzerPreset en VideoAnalyzerPreset in Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: c00af3a128685dfbd2435b65fe4d00107ca22ba4
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94744354"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Video-en audio bestanden analyseren met Azure Media Services

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Met Azure Media Services v3 kunt u inzichten uit uw video-en audio bestanden ophalen met Video Indexer. In dit artikel worden de Media Services v3 Analyzer-voor waarden beschreven die worden gebruikt om deze inzichten te extra heren. Als u meer gedetailleerde inzichten wilt, gebruikt u Video Indexer rechtstreeks. Bekijk het [vergelijkings document](../video-indexer/compare-video-indexer-with-media-services-presets.md)als u wilt weten wanneer de voor instellingen van Video Indexer versus Media Services Analyzer moeten worden gebruikt.

Er zijn twee modi voor de Audio Analyzer vooraf, basis en standaard. Zie de beschrijving van de verschillen in de volgende tabel.

Als u uw inhoud wilt analyseren met Media Services v3-voor instellingen, maakt u een **trans formatie** en verzendt u een **taak** die gebruikmaakt van een van deze voor instellingen: [VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset) of **AudioAnalyzerPreset**. Zie [Video's analyseren met Azure Media Services](analyze-videos-tutorial-with-api.md)voor een zelf studie waarin wordt uitgelegd hoe u **VideoAnalyzerPreset** kunt gebruiken.

> [!NOTE]
> Wanneer u een video-of audio analyse-voor instelling gebruikt, gebruikt u de Azure Portal om uw account in te stellen op 10 S3-media gereserveerde eenheden, hoewel dit niet vereist is. U kunt S1 of S2 voor voor keuren voor audio gebruiken. Zie [Mediaverwerking schalen](media-reserved-units-cli-how-to.md) voor meer informatie.

## <a name="compliance-privacy-and-security"></a>Compliance, privacy en beveiliging

Het is heel belangrijk dat u zich realiseert dat u zich bij het gebruik van Video Indexer moet houden aan alle toepasselijke wetgeving en dat het niet is toegestaan Video Indexer of een andere Azure-service te gebruiken op een manier die de rechten van anderen schendt of anderen schade berokkent. Voordat u video's, met inbegrip van eventuele biometrische gegevens, uploadt naar de Video Indexer-service om daar te worden verwerkt of opgeslagen, moet u over alle daarvoor benodigde rechten beschikken, waaronder alle toepasselijke toestemmingen van de persoon of personen in de video. Meer informatie over compliance, privacy en beveiliging in Video Indexer vindt u in de [voorwaarden van Microsoft Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Als u meer wilt weten over de privacyverplichtingen die Microsoft hanteert ten aanzien van uw gegevens, kunt u de [Privacyverklaring](https://privacy.microsoft.com/PrivacyStatement) van Microsoft, de [Voorwaarden voor Online Diensten](https://www.microsoft.com/licensing/product-licensing/products) ('OST') en het [Addendum met betrekking tot gegevensverwerking](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ('DPA') raadplegen. Aanvullende informatie over privacy, waaronder informatie over de bewaarplicht voor gegevens en over de verwijdering/vernietiging ervan, is beschikbaar in de Voorwaarden voor Online Diensten (OST) en [hier](../video-indexer/faq.md). Door Video Indexer te gebruiken, gaat u akkoord met de voorwaarden van Cognitive Services, de OST, DPA en de Privacyverklaring.

## <a name="built-in-presets"></a>Ingebouwde voor instellingen

Media Services ondersteunt momenteel de volgende ingebouwde Analyzer-voor waarden:  

|**Naam van voor instelling**|**Scenario**|**Details**|
|---|---|---|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Audio Standard analyseren|De voor instelling past een vooraf gedefinieerde set op AI-gebaseerde analyse bewerkingen toe, waaronder spraak transcriptie. Op dit moment ondersteunt de vooraf ingestelde verwerking van inhoud met één audio track die spraak in één taal bevat. U kunt de taal voor de audio lading in de invoer opgeven met de BCP-47-indeling van de taal code-regio. Ondersteunde talen zijn Engels (' en-US ' en ' en ' en ' en '), Spaans (' es-ES ' en ' es-MX '), Frans (' fr-FR '), Italiaans (' it-IT '), Japans (' ja-JP '), Portugees (' pt-BR '), Chinees (' zh-CN '), Duits (' de '), Arabisch (' ar-EG ' en ' ar-SY '), Russisch (' ru-RU '), Hindi (' Hi-IN ') en Koreaans (' ko-KR ').<br/><br/> Als de taal niet is opgegeven of is ingesteld op NULL, kiest automatische taal detectie de eerst gedetecteerde taal en wordt de geselecteerde taal voor de duur van het bestand voortgezet. De functie voor automatische taal detectie ondersteunt momenteel Engels, Chinees, Frans, Duits, Italiaans, Japans, Spaans, Russisch en Portugees. Het is niet mogelijk om dynamische switches te scha kelen tussen talen nadat de eerste taal is gedetecteerd. De functie voor automatische taal detectie werkt het beste met geluids opnamen met duidelijk waarneembaar spraak. Als de taal niet kan worden gevonden met de automatische taal detectie, valt de transcriptie terug naar Engels.|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Audio-basis analyseren|Deze modus voert spraak-naar-tekst transcriptie en genereert een ondertitelings bestand van VTT. De uitvoer van deze modus bevat een Insights-JSON-bestand met alleen de tref woorden, transcriptie en timing gegevens. Automatische taal detectie en diarization van de spreker zijn niet opgenomen in deze modus. De lijst met ondersteunde talen is [hier](https://go.microsoft.com/fwlink/?linkid=2109463) beschikbaar|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Audio en video analyseren|Extraheert inzichten (Rich meta data) van audio en video en voert een JSON-indelings bestand uit. U kunt opgeven of u alleen geluids inzichten wilt extra heren tijdens het verwerken van een video bestand. Zie [video analyseren](analyze-videos-tutorial-with-api.md)voor meer informatie.|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|In video aanwezige gezichten detecteren|Hierin worden de instellingen beschreven die moeten worden gebruikt bij het analyseren van een video om alle gezichten te detecteren die aanwezig zijn.|

### <a name="audioanalyzerpreset-standard-mode"></a>AudioAnalyzerPreset standaard modus

Met de voor instelling kunt u meerdere geluids inzichten uit een audio-of video bestand ophalen.

De uitvoer bevat een JSON-bestand (met alle inzichten) en het VTT-bestand voor de transcriptie van audio. Deze standaard instelling accepteert een eigenschap die de taal van het invoer bestand opgeeft in de vorm van een [BCP47](https://tools.ietf.org/html/bcp47) teken reeks. De geluids inzichten zijn onder andere:

* **Audio-transcriptie**: een transcriptie van de gesp roken woorden met tijds tempels. Meerdere talen worden ondersteund.
* **Sprekers indexering**: een toewijzing van de luid sprekers en de bijbehorende gesp roken woorden.
* **Speech sentiment Analysis**: de uitvoer van sentiment-analyse die wordt uitgevoerd op de audio transcriptie.
* **Tref woorden**: tref woorden die worden geëxtraheerd uit de audio-transcriptie.

### <a name="audioanalyzerpreset-basic-mode"></a>AudioAnalyzerPreset Basic-modus

Met de voor instelling kunt u meerdere geluids inzichten uit een audio-of video bestand ophalen.

De uitvoer bevat een JSON-bestand en het VTT-bestand voor de transcripten van de audio. Deze standaard instelling accepteert een eigenschap die de taal van het invoer bestand opgeeft in de vorm van een [BCP47](https://tools.ietf.org/html/bcp47) teken reeks. De uitvoer omvat:

* **Audio-transcriptie**: een transcriptie van de gesp roken woorden met tijds tempels. Meerdere talen worden ondersteund, maar automatische taal detectie en diarization van de spreker worden niet opgenomen.
* **Tref woorden**: tref woorden die worden geëxtraheerd uit de audio-transcriptie.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Met de voor instelling kunt u meerdere audio-en video inzichten uit een video bestand ophalen. De uitvoer bevat een JSON-bestand (met alle inzichten), een VTT-bestand voor de getranscripteerde video en een verzameling miniaturen. Deze vooraf ingestelde accepteert ook een [BCP47](https://tools.ietf.org/html/bcp47) teken reeks (die de taal van de video vertegenwoordigt) als een eigenschap. De video inzichten bevatten alle hierboven genoemde audio inzichten en de volgende bijkomende items:

* **Gezichts tracking**: de tijd gedurende welke gezichten aanwezig zijn in de video. Elk gezicht heeft een face-ID en een bijbehorende verzameling miniaturen.
* **Visuele tekst**: de tekst die wordt gedetecteerd via optische teken herkenning. De tekst is een tijds tempel en wordt ook gebruikt voor het extra heren van tref woorden (naast de transcripten van audio).
* **Keyframes**: een verzameling keyframes die uit de video is geëxtraheerd.
* **Visuele toezicht op inhoud**: het gedeelte van de Video's dat is gemarkeerd als volwassene of ongepaste.
* **Aantekening**: een resultaat van het aantekenen van de Video's op basis van een vooraf gedefinieerd object model

## <a name="insightsjson-elements"></a>insights.jsop elementen

De uitvoer bevat een JSON-bestand (insights.jsaan) met alle inzichten die in de video of audio zijn gevonden. De JSON kan de volgende elementen bevatten:

### <a name="transcript"></a>verslag

|Naam|Beschrijving|
|---|---|
|id|De regel-ID.|
|tekst|De transcriptie zelf.|
|language|De transcript taal. Bedoeld ter ondersteuning van transcripten waarbij elke regel een andere taal kan hebben.|
|vaak|Een lijst met peri Oden waarin deze regel wordt weer gegeven. Als de instantie een transcript heeft, heeft deze slechts één exemplaar.|

Voorbeeld:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>optische

|Naam|Beschrijving|
|---|---|
|id|De OCR-regel-ID.|
|tekst|De OCR-tekst.|
|betrouwbaarheid|Het vertrouwens niveau van de herkenning.|
|language|De OCR-taal.|
|vaak|Een lijst met peri Oden waarin deze OCR is verschenen (dezelfde OCR kan meerdere keren voor komen).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>aanhoudende

|Naam|Beschrijving|
|---|---|
|id|De face-ID.|
|naam|De naam van het gezicht. Dit kan ' onbekend #0 ', een geïdentificeerde beroemdheden of een door de klant getrainde persoon zijn.|
|betrouwbaarheid|De gezichts-id-betrouw baarheid.|
|beschrijving|Een beschrijving van de beroemdheden. |
|thumbnailId|De ID van de miniatuur van het gezicht.|
|knownPersonId|De interne ID (als deze een bekende persoon is).|
|referenceId|De Bing-ID (als het een Bing-beroemdheden is).|
|Type|Momenteel alleen Bing.|
|titel|De titel (als het een beroemdheden is, bijvoorbeeld ' micro soft ' CEO ').|
|imageUrl|De afbeeldings-URL, als het een beroemdheden is.|
|vaak|Exemplaren waarvan het gezicht in het opgegeven tijds bereik is verschenen. Elk exemplaar heeft ook een thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>afzonderlijke

|Naam|Beschrijving|
|---|---|
|id|De opname-ID.|
|Keyframes|Een lijst met keyframes in de foto (elk heeft een ID en een lijst met tijds bereik exemplaren). Key frames-instanties hebben een thumbnailId-veld met de miniatuur-ID van het keyframe.|
|vaak|Een lijst met tijds bereiken van deze opname (afbeeldingen hebben slechts één exemplaar).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>statistieken

|Naam|Beschrijving|
|---|---|
|CorrespondenceCount|Aantal correspondentie in de video.|
|WordCount|Het aantal woorden per spreker.|
|SpeakerNumberOfFragments|De hoeveelheid fragmenten die de spreker in een video heeft.|
|SpeakerLongestMonolog|De langste monolog van de spreker. Als de spreker stiltes bevat in de monolog, wordt deze opgenomen. Stilte aan het begin en het einde van de monolog wordt verwijderd.|
|SpeakerTalkToListenRatio|De berekening is gebaseerd op de tijd die is besteed aan de monolog van de spreker (zonder de stilte in tussen) gedeeld door de totale tijd van de video. De tijd wordt afgerond op het derde decimale punt.|


### <a name="sentiments"></a>gevoel

Gevoel worden geaggregeerd met het veld sentimentType (positief/neutraal/negatief). Bijvoorbeeld: 0-0,1, 0,1-0,2.

|Naam|Beschrijving|
|---|---|
|id|De sentiment-ID.|
|averageScore |Het gemiddelde van alle scores van alle exemplaren van dat sentiment type-positief/neutraal/negatief|
|vaak|Een lijst met tijds bereikn waar deze sentiment verscheen.|
|sentimentType |Het type kan ' positief ', ' neutraal ' of ' negatief ' zijn.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>Labels

|Naam|Beschrijving|
|---|---|
|id|De label-ID.|
|naam|De naam van het label (bijvoorbeeld ' computer ', ' TV ').|
|language|De naam taal van het label (bij omzetting). BCP-47|
|vaak|Een lijst met tijds bereiken waar dit label wordt weer gegeven (een label kan meerdere keren voor komen). Elk exemplaar heeft een veld betrouw baarheid. |

```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>trefwoorden

|Naam|Beschrijving|
|---|---|
|id|De ID van het sleutel woord.|
|tekst|De tekst van het sleutel woord.|
|betrouwbaarheid|Het vertrouwens niveau van het tref woord.|
|language|De taal van het tref woord (tijdens het vertalen).|
|vaak|Een lijst met tijds bereiken waar dit tref woord is opgetreden (een tref woord kan meerdere keren voor komen).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

Het visualContentModeration-blok bevat Peri Oden die Video Indexer mogelijk inhoud voor volwassenen hebben gevonden. Als visualContentModeration leeg is, is er geen inhoud voor volwassenen gevonden.

Video's die een inhoud van volwassenen of ongepaste bevatten, zijn mogelijk alleen beschikbaar voor de persoonlijke weer gave. Gebruikers kunnen een aanvraag indienen voor een menselijke beoordeling van de inhoud. in dat geval `IsAdult` bevat het kenmerk het resultaat van de menselijke beoordeling.

|Naam|Beschrijving|
|---|---|
|id|De controle-ID van de visuele inhoud.|
|adultScore|De volwassen Score (van content moderator).|
|racyScore|De ongepaste-Score (van inhouds toezicht).|
|vaak|Een lijst met peri Oden waarin deze visuele elementen worden gevisualiseerd.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: Video's analyseren met Azure Media Services](analyze-videos-tutorial-with-api.md)
