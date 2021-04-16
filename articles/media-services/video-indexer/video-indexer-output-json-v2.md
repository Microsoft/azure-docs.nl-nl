---
title: Bekijk de Video Indexer geproduceerd door v2 API - Azure
titleSuffix: Azure Media Services
description: In dit onderwerp worden de Azure Media Services Video Indexer geproduceerd door v2 API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: 84bb4766b3a896823dd0bef023f8042965a85846
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532861"
---
# <a name="examine-the-video-indexer-output"></a>De uitvoer van Video Indexer bekijken

Wanneer een video wordt geïndexeerd, Video Indexer de JSON-inhoud geproduceerd die details van de opgegeven video-inzichten bevat. De inzichten omvatten: transcripten, OCR's, gezichten, onderwerpen, blokken, enzovoort. Elk inzichttype bevat exemplaren van tijdsbereiken die laten zien wanneer het inzicht in de video wordt weergegeven. 

U kunt de samengevatte inzichten van de video visueel bekijken door op de knop **Afspelen** te drukken op de video op [de Video Indexer website.](https://www.videoindexer.ai/) 

U kunt de API ook gebruiken door de GET **Video Index-API** aan te roepen en de antwoordstatus is OK. U krijgt een gedetailleerde JSON-uitvoer als antwoordinhoud.

![Inzichten](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

In dit artikel wordt de Video Indexer uitvoer (JSON-inhoud) onderzocht. <br/>Zie inzichten voor meer informatie over welke functies en inzichten [voor u Video Indexer beschikbaar zijn.](video-indexer-overview.md#video-insights)

> [!NOTE]
> De vervaldatum van alle toegangstokens in Video Indexer is één uur.

## <a name="get-the-insights"></a>De inzichten verkrijgen

### <a name="insightsoutput-produced-in-the-websiteportal"></a>Inzichten/uitvoer die zijn geproduceerd in de website/portal

1. Ga naar de [Video Indexer](https://www.videoindexer.ai/)-website en meld u aan.
1. Zoek een video met de uitvoer die u wilt bekijken.
1. Druk op **Afspelen**.
1. Selecteer het **tabblad Inzichten** (samengevatte inzichten) of het tabblad **Tijdlijn** (hiermee kunt u de relevante inzichten filteren).
1. Download artefacten en wat er in staat.

Zie Video-inzichten [weergeven en bewerken voor meer informatie.](video-indexer-view-edit.md)

## <a name="insightsoutput-produced-by-api"></a>Inzichten/uitvoer geproduceerd door API

1. Als u het JSON-bestand wilt ophalen, roept u [de Get Video Index-API aan](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index)
1. Als u ook geïnteresseerd bent in specifieke artefacten, roept u de [DOWNLOAD-URL-API voor videoartefacten downloaden aan](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Artifact-Download-Url)

    Geef in de API-aanroep het aangevraagde artefacttype (OCR, gezichten, sleutelframes, enzovoort) op

## <a name="root-elements-of-the-insights"></a>Hoofdelementen van de inzichten

|Naam|Beschrijving|
|---|---|
|accountId|De VI-account-id van de afspeellijst.|
|id|De id van de afspeellijst.|
|naam|De naam van de afspeellijst.|
|beschrijving|De beschrijving van de afspeellijst.|
|userName|De naam van de gebruiker die de afspeellijst heeft gemaakt.|
|Gemaakt|De aanmaaktijd van de afspeellijst.|
|privacyMode|De privacymodus van de afspeellijst (privé/openbaar).|
|staat|De afspeellijst (geüpload, verwerkt, verwerkt, mislukt, in quarantaine geplaatst).|
|isOwned|Geeft aan of de afspeellijst is gemaakt door de huidige gebruiker.|
|isEditable|Geeft aan of de huidige gebruiker gemachtigd is om de afspeellijst te bewerken.|
|isBase|Geeft aan of de afspeellijst een basisplaylist (een video) of een afspeellijst is die bestaat uit andere (afgeleide) video's.|
|durationInSeconds|De totale duur van de afspeellijst.|
|summarizedInsights|Bevat één [samengevatteInsights.](#summarizedinsights)
|video's|Een lijst met [video's die](#videos) de afspeellijst samenstellen.<br/>Als deze afspeellijst van samengestelde tijdsbereiken van andere (afgeleide) video's, bevatten de video's in deze lijst alleen gegevens uit de opgenomen tijdsbereiken.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

In deze sectie ziet u de samenvatting van de inzichten.

|Kenmerk | Beschrijving|
|---|---|
|naam|De naam van de video. Bijvoorbeeld: Azure Monitor.|
|id|De id van de video. Bijvoorbeeld 63c6d532ff.|
|privacyMode|Uw uitsplitsing kan een van de volgende modi hebben: **Privé,** **Openbaar.** **Openbaar:** de video is zichtbaar voor iedereen in uw account en voor iedereen die een koppeling naar de video heeft. **Privé:** de video is zichtbaar voor iedereen in uw account.|
|duur|Bevat één duur waarin wordt beschreven hoe lang een inzicht heeft plaatsgevonden. De duur is in seconden.|
|thumbnailVideoId|De id van de video van waaruit de miniatuur is gemaakt.
|thumbnailId|De miniatuur-id van de video. Als u de werkelijke miniatuur wilt op halen, roept [u Get-Thumbnail](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) aan en geef u deze thumbnailVideoId en thumbnailId door.|
|faces/animatedCharacters|Kan nul of meer gezichten bevatten. Zie [gezichten/animatedCharacters voor meer informatie.](#facesanimatedcharacters)|
|trefwoorden|Kan nul of meer trefwoorden bevatten. Zie trefwoorden voor meer [informatie.](#keywords)|
|Gevoelens|Kan nul of meer gevoelens bevatten. Zie sentimenten voor [meer informatie.](#sentiments)|
|audioEffects| Kan nul of meer audio-effecten bevatten. Zie [audioEffects](#audioeffects-public-preview)voor meer informatie.|
|labels| Kan nul of meer labels bevatten. Zie labels voor [gedetailleerdere informatie.](#labels)|
|Merken| Kan nul of meer merken bevatten. Zie merken voor meer [informatie.](#brands)|
|statistieken | Zie statistieken voor meer [informatie.](#statistics)|
|Emoties| Kan nul of meer emoties bevatten. Zie emoties voor meer [gedetailleerde informatie.](#emotions)|
|Onderwerpen|Kan nul of meer onderwerpen bevatten. Het [inzicht in](#topics) de onderwerpen.|

## <a name="videos"></a>video's

|Naam|Beschrijving|
|---|---|
|accountId|De VI-account-id van de video.|
|id|De id van de video.|
|naam|De naam van de video.
|staat|De status van de video (geüpload, verwerkt, verwerkt, mislukt, in quarantaine geplaatst).|
|processingProgress|De voortgang van de verwerking tijdens de verwerking (bijvoorbeeld 20%).|
|failureCode|De foutcode als deze niet kan worden verwerkt (bijvoorbeeld 'UnsupportedFileType').|
|failureMessage|Het foutbericht als deze niet kan worden verwerkt.|
|externalId|De externe id van de video (indien opgegeven door de gebruiker).|
|externalUrl|De externe URL van de video (indien opgegeven door de gebruiker).|
|metagegevens|De externe metagegevens van de video (indien opgegeven door de gebruiker).|
|isAdult|Geeft aan of de video handmatig is beoordeeld en geïdentificeerd als een video voor volwassenen.|
|Inzichten|Het inzichtobject. Zie inzichten voor [meer informatie.](#insights)|
|thumbnailId|De miniatuur-id van de video. Als u de werkelijke miniatuur wilt op halen, roept [u Get-Thumbnail](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) aan en gebruikt u de video-id en thumbnailId.|
|publishedUrl|Een URL voor het streamen van de video.|
|publishedUrlProxy|Een URL voor het streamen van de video van (voor Apple-apparaten).|
|viewToken|Een weergave-token met korte duur voor het streamen van de video.|
|sourceLanguage|De brontaal van de video.|
|language|De werkelijke taal (vertaling) van de video.|
|indexingPreset|De voorinstelling die wordt gebruikt om de video te indexeren.|
|streamingPreset|De voorinstelling die wordt gebruikt om de video te publiceren.|
|linguisticModelId|Het CRIS-model dat wordt gebruikt om de video te transcriberen.|
|statistieken | Zie statistieken voor [meer informatie.](#statistics)|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>Inzichten

Elk inzicht (bijvoorbeeld transcriptregels, gezichten, merken, enzovoort) bevat een lijst met unieke elementen (bijvoorbeeld face1, face2, face3) en elk element heeft zijn eigen metagegevens en een lijst met de instanties ervan (dit zijn tijdsbereiken met aanvullende optionele metagegevens).

Een gezicht kan een id, een naam, een miniatuur, andere metagegevens en een lijst met tijdelijke exemplaren hebben (bijvoorbeeld: 00:00:05 – 00:00:10, 00:01:00 - 00:02:30 en 00:41:21 – 00:41:49.) Elk tijdelijke exemplaar kan aanvullende metagegevens hebben. Bijvoorbeeld de rechthoekcoördinaten van het gezicht (20.230,60,60).

|Versie|De codeversie|
|---|---|
|sourceLanguage|De brontaal van de video (uitgaande van één hoofdtaal). In de vorm van een [BCP-47-tekenreeks.](https://tools.ietf.org/html/bcp47)|
|language|De inzichtentaal (vertaald vanuit de brontaal). In de vorm van een [BCP-47-tekenreeks.](https://tools.ietf.org/html/bcp47)|
|Afschrift|Het [transcriptinzicht.](#transcript)|
|Ocr|Het [OCR-inzicht.](#ocr)|
|trefwoorden|Het [inzicht in](#keywords) trefwoorden.|
|blokken|Kan een of meer blokken [bevatten](#blocks)|
|faces/animatedCharacters|Het [inzicht gezichten/animatieGrafieken.](#facesanimatedcharacters)|
|labels|Het [inzicht in](#labels) labels.|
|Shots|Het [inzicht in de](#shots) opnamen.|
|Merken|Het [inzicht in](#brands) de merken.|
|audioEffects|Het [inzicht audioEffects.](#audioeffects-public-preview)|
|Gevoelens|Het [gevoelsinzicht.](#sentiments)|
|visualContentModeration|Het [inzicht visualContentModeration.](#visualcontentmoderation)|
|textualContentModeration|Het [inzicht textualContentModeration.](#textualcontentmoderation)|
|Emoties| Het [inzicht in emoties.](#emotions)|
|Onderwerpen|Inzicht [in de](#topics) onderwerpen.|
|luidsprekers|Het [inzicht in de](#speakers) sprekers.|

Voorbeeld:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>blokken

Kenmerk | Beschrijving
---|---
id|Id van het blok.|
Exemplaren|Een lijst met tijdsbereiken van dit blok.|

#### <a name="transcript"></a>Afschrift

|Naam|Beschrijving|
|---|---|
|id|De regel-id.|
|tekst|De transcriptie zelf.|
|betrouwbaarheid|De nauwkeurigheid van de transcriptie.|
|speakerId|De id van de spreker.|
|language|De transcripttaal. Bedoeld ter ondersteuning van transcriptie waarbij elke regel een andere taal kan hebben.|
|Exemplaren|Een lijst met tijdsbereiken waarin deze regel wordt weergegeven. Als het exemplaar transcriptie is, heeft deze slechts één exemplaar.|

Voorbeeld:

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "text":"ignite 2016. Your mission at Microsoft is to empower every",
  "confidence":0.8944,
  "speakerId":2,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
```

#### <a name="ocr"></a>Ocr

|Naam|Beschrijving|
|---|---|
|id|De regel-id van OCR.|
|tekst|De OCR-tekst.|
|betrouwbaarheid|Het herkenningsvertrouwen.|
|language|De OCR-taal.|
|Exemplaren|Een lijst met tijdsbereiken waarin deze OCR wordt weergegeven (dezelfde OCR kan meerdere keren worden weergegeven).|
|hoogte|De hoogte van de OCR-rechthoek|
|top|De bovenste locatie in px|
|links| De linkerlocatie in px|
|breedte|De breedte van de OCR-rechthoek|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>trefwoorden

|Naam|Beschrijving|
|---|---|
|id|De trefwoord-id.|
|tekst|De trefwoordtekst.|
|betrouwbaarheid|Het herkenningsvertrouwen van het sleutelwoord.|
|language|De trefwoordtaal (indien vertaald).|
|Exemplaren|Een lijst met tijdsbereiken waarin dit trefwoord wordt weergegeven (een trefwoord kan meerdere keren worden weergegeven).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="facesanimatedcharacters"></a>faces/animatedCharacters

`animatedCharacters` -element wordt vervangen `faces` als de video is geïndexeerd met een model met animaties. Dit wordt gedaan met behulp van een aangepast model in Custom Vision, Video Indexer wordt uitgevoerd op keyframes.

Als er gezichten (geen animaties) aanwezig zijn, Video Indexer face-API gebruikt voor alle frames van de video om gezichten en beroemdheden te detecteren.

|Naam|Beschrijving|
|---|---|
|id|De gezichts-id.|
|naam|De naam van het gezicht. Dit kan 'Onbekende #0, een geïdentificeerde beroemdheid of een door de klant getraind persoon zijn.|
|betrouwbaarheid|De betrouwbaarheid van gezichtsidentificatie.|
|beschrijving|Een beschrijving van de beroemdheid. |
|thumbnailId|De id van de miniatuur van dat gezicht.|
|knownPersonId|Als het een bekende persoon is, de interne id.|
|referenceId|Als het een Bing-beroemdheid is, is dit de Bing-id.|
|referenceType|Op dit moment alleen Bing.|
|titel|Als het een beroemdheid is, de titel (bijvoorbeeld 'Microsoft's CEO').|
|imageUrl|Als het een beroemdheid is, de afbeeldings-URL.|
|Exemplaren|Dit zijn exemplaren van waar het gezicht in het opgegeven tijdsbereik wordt weer gegeven. Elk exemplaar heeft ook een thumbnailsId. |

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

#### <a name="labels"></a>labels

|Naam|Beschrijving|
|---|---|
|id|De label-id.|
|naam|De labelnaam (bijvoorbeeld 'Computer', 'TV').|
|language|De taal van de labelnaam (indien vertaald). BCP-47|
|Exemplaren|Een lijst met tijdsbereiken waarin dit label wordt weergegeven (een label kan meerdere keren worden weergegeven). Elk exemplaar heeft een vertrouwensveld. |


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

#### <a name="scenes"></a>Scènes

|Naam|Beschrijving|
|---|---|
|id|De scène-id.|
|Exemplaren|Een lijst met tijdsbereiken van deze scène (een scène kan slechts één exemplaar hebben).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>Shots

|Naam|Beschrijving|
|---|---|
|id|De schermafbeeldings-id.|
|Hoofdframes|Een lijst met keyFrames in de opname (elk heeft een id en een lijst met tijdsbereiken van exemplaren). Elk keyFrame-exemplaar heeft een thumbnailId-veld dat de miniatuur-id van het keyFrame bevat.|
|Exemplaren|Een lijst met tijdsbereiken van deze opname (een opname kan slechts één exemplaar hebben).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>Merken

Merknamen voor bedrijven en producten gedetecteerd in de spraak-naar-teksttranscriptie en/of Video OCR. Dit omvat geen visuele herkenning van merken of logodetectie.

|Naam|Beschrijving|
|---|---|
|id|De merk-id.|
|naam|De naam van het merk.|
|referenceId | Het achtervoegsel van de wikipedia-URL van het merk. 'Target_Corporation' is bijvoorbeeld het achtervoegsel van [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) .
|referenceUrl | De Wikipedia-URL van het merk, indien aanwezig. Bijvoorbeeld [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) .
|beschrijving|De beschrijving van het merk.|
|tags|Een lijst met vooraf gedefinieerde tags die aan dit merk zijn gekoppeld.|
|betrouwbaarheid|De betrouwbaarheidswaarde van de Video Indexer merkdetector (0-1).|
|Exemplaren|Een lijst met tijdsbereiken van dit merk. Elk exemplaar heeft een brandType, dat aangeeft of dit merk in het transcript of in OCR wordt vermeld.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statistieken

|Naam|Beschrijving|
|---|---|
|CorrespondenceCount|Het aantalcorrespondenten in de video.|
|SpeakerWordCount|Het aantal woorden per spreker.|
|SpeakerNumberOfFragments|De hoeveelheid fragmenten die de spreker in een video heeft.|
|SpeakerLongestMonolog|De langste monolog van de spreker. Als de spreker stiltes in de monologe heeft, wordt deze opgenomen. Stilte aan het begin en het einde van de monologo wordt verwijderd.| 
|SpeakerTalkToListenRatio|De berekening is gebaseerd op de tijd die is besteed aan de monologie van de spreker (zonder de stilte daartussenin) gedeeld door de totale tijd van de video. De tijd wordt afgerond op het derde decimaalteken.|

#### <a name="audioeffects-public-preview"></a>audioEffects (openbare preview)

|Naam|Beschrijving
|---|---|
|id|De id van het audio-effect|
|type|Het type audio-effect|
|Exemplaren|Een lijst met tijdsbereiken waarin dit audio-effect wordt weergegeven. Elk exemplaar heeft een vertrouwensveld.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Siren",
    "instances": [
    {
       "confidence": 0.87,
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
       "confidence": 0.87,
       "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>Gevoelens

Sentimenten worden geaggregeerd door het veld sentimentType (positief/neutraal/negatief). Bijvoorbeeld 0-0.1, 0.1-0.2.

|Naam|Beschrijving|
|---|---|
|id|De gevoels-id.|
|averageScore |Het gemiddelde van alle scores van alle exemplaren van dat gevoelstype - positief/neutraal/negatief|
|Exemplaren|Een lijst met tijdsbereiken waarin dit gevoel wordt weergegeven.|
|sentimentType |Het type kan 'Positief', 'Neutraal' of 'Negatief' zijn.|

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

#### <a name="visualcontentmoderation"></a>visualContentModeration

Het blok visualContentModeration bevat tijdsbereiken die Video Indexer mogelijk inhoud voor volwassenen bevatten. Als visualContentModeration leeg is, is er geen inhoud voor volwassenen geïdentificeerd.

Video's die inhoud voor volwassenen of erotische inhoud bevatten, zijn mogelijk alleen beschikbaar voor persoonlijke weergave. Gebruikers hebben de mogelijkheid om een aanvraag in te dienen voor een menselijke beoordeling van de inhoud. In dat geval bevat het kenmerk IsAdult het resultaat van de menselijke beoordeling.

|Naam|Beschrijving|
|---|---|
|id|De id voor het modereren van visuele inhoud.|
|adultScore|De score voor volwassenen (van content moderator).|
|racyScore|De racy score (van inhoudsbeheer).|
|Exemplaren|Een lijst met tijdsbereiken waarin dit visuele inhoudsbeheer werd weergegeven.|

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

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Naam|Beschrijving|
|---|---|
|id|De tekstuele inhoudsbeheer-id.|
|bannedWordsCount |Het aantal verboden woorden.|
|bannedWordsRatio |De verhouding van het totale aantal woorden.|

#### <a name="emotions"></a>Emoties

Video Indexer identificeert emoties op basis van spraak- en audio-cues. De geïdentificeerde emotie kan zijn: kunnen zijn: kunnen zijn: heen en weer gemoed, kwaadheid of angst.

|Naam|Beschrijving|
|---|---|
|id|De emotie-id.|
|type|Het emotiemoment dat is geïdentificeerd op basis van spraak- en audio-aanwijzingen. De emotie kan zijn: emoties, angst, angst of angst.|
|Exemplaren|Een lijst met tijdsbereiken waar deze emotie werd weergegeven.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>Onderwerpen

Video Indexer worden belangrijke onderwerpen uit transcripties defeit. Indien mogelijk wordt de IPTC-taxonomie op het [tweede](https://iptc.org/standards/media-topics/) niveau opgenomen. 

|Naam|Beschrijving|
|---|---|
|id|De onderwerp-id.|
|naam|De onderwerpnaam, bijvoorbeeld: 'Farmaceutische producten'.|
|referenceId|Breadcrumbs die de onderwerpenhiërarchie weerspiegelen. Bijvoorbeeld: "Gezondheid en gezondheid /medicijnen en gezondheidszorg/farmaceutische producten".|
|betrouwbaarheid|De betrouwbaarheidsscore in het bereik [0,1]. Hoger is zekerder.|
|language|De taal die in het onderwerp wordt gebruikt.|
|iptcName|De naam van de IPTC-mediacode, indien gedetecteerd.|
|Exemplaren |Op dit Video Indexer onderwerp niet geïndexeerd op tijdsintervallen, zodat de hele video wordt gebruikt als interval.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

#### <a name="speakers"></a>luidsprekers

|Naam|Beschrijving|
|---|---|
|id|De spreker-id.|
|naam|De sprekernaam in de vorm van 'Speaker *<number>* #' bijvoorbeeld: 'Speaker #1'.|
|Exemplaren |Een lijst met tijdsbereiken waar deze spreker is verschenen.|

```json
"speakers":[
{
  "id":1,
  "name":"Speaker #1",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "name":"Speaker #2",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
` ` `
```
## <a name="next-steps"></a>Volgende stappen

[Video Indexer-ontwikkelaarsportal](https://api-portal.videoindexer.ai)

Zie Embed Video Indexer widgets into your applications (Widgets insluiten in uw toepassingen) voor meer informatie over het insluiten van [widgets in uw toepassing.](video-indexer-embed-widgets.md) 

