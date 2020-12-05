---
title: Migreren van v2 naar v3 REST API-Speech-Service
titleSuffix: Azure Cognitive Services
description: Vergeleken met v2 bevat de nieuwe API-versie V3 een set kleinere belang rijke wijzigingen. Dit document helpt u in geen enkele tijd te migreren naar de nieuwe primaire versie.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1dae963781cc0caacc25938e700a4c70a1f51a
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96737991"
---
# <a name="migration-from-v20-to-v30-of-speech-to-text-rest-api"></a>Migratie van v 2.0 naar v 3.0 van spraak naar tekst REST API

De V3-versie van de spraak REST API verbetert de vorige API-versie ten opzichte van betrouw baarheid en gebruiks gemak. De API-indeling wordt nauw keuriger uitgelijnd met andere Azure-of Cognitive Services-API's. Dit helpt u bij het Toep assen van uw bestaande vaardig heden wanneer u de spraak-API gebruikt.

Het overzicht van de API is beschikbaar als [Swagger-document](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Dit is ideaal om u een API-overzicht te geven en de nieuwe API te testen.

We bieden voor beelden voor C# en python. Voor batch-transcripties vindt u de voor beelden in de [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples) in de `samples/batch` submap.

## <a name="forward-compatibility"></a>Compatibiliteit door sturen

Om te zorgen voor een soepele migratie naar v3, kunnen alle entiteiten van v2 ook worden gevonden in de V3 API onder dezelfde identiteit. Als er een schema wijziging van het resultaat is (bijvoorbeeld transcripties), worden de antwoorden voor een GET in v3-versie van de API in het v3-schema weer geven. Als u de GET in v2-versie van de API uitvoert, heeft het resultaat schema de v2-indeling. Nieuw gemaakte entiteiten op v3 **zijn niet** beschikbaar op v2.

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

De lijst met belang rijke wijzigingen is gesorteerd op basis van de grootte van wijzigingen die nodig zijn om aan te passen. Er zijn slechts een paar wijzigingen waarvoor een niet-gelastige wijziging in de aanroepende code is vereist. Voor de meeste wijzigingen is eenvoudige hernoemen vereist. De tijd die nodig is om teams te migreren van v2 naar v3 variëren tussen enkele uur en een paar dagen. De voor delen van verbeterde stabiliteit, eenvoudigere code, snellere reacties worden de investering echter snel verschoven. 

### <a name="host-name-changes"></a>Gewijzigde namen van hosts

De hostnamen zijn gewijzigd van {Region}. cri's. AI naar {Region}. API. cognitieve. Microsoft. com. In deze wijziging bevatten de paden niet meer ' API/', omdat deze deel uitmaakt van de hostnaam. Zie het [Swagger-document](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) voor een volledige beschrijving van regio's en paden.

### <a name="identity-of-an-entity"></a>Identiteit van een entiteit

De eigenschap `id` is vervangen door `self` . In v2 moest een API-gebruiker weten hoe onze paden op de API worden gemaakt. Dit was niet-uitbreidbaar en vereist overbodig werk van de gebruiker. De eigenschap `id` (UUID) wordt vervangen door `self` (teken reeks), de locatie van de entiteit (URL). De waarde is nog uniek tussen alle entiteiten. Als `id` is opgeslagen als een teken reeks in uw code, is een eenvoudige naam wijziging voldoende voor de ondersteuning van het nieuwe schema. U kunt nu de `self` inhoud als URL gebruiken voor alle rest-aanroepen voor uw entiteit (Get, patch, Delete).

Als voor de entiteit extra functionaliteit beschikbaar is onder andere paden, worden deze weer gegeven onder `links` . Een goed voor beeld is een transcriptie die een afzonderlijke methode heeft voor `GET` de inhoud van de transcriptie.

v2 transcriptie:

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

V3 transcriptie:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

Afhankelijk van de implementatie van uw client is het mogelijk niet voldoende om de naam van de eigenschap te wijzigen. We raden u aan gebruik te maken van de geretourneerde waarden van `self` en `links` als de doel-url's van uw rest-aanroepen en niet om de paden in uw-client te genereren. Door de geretourneerde Url's te gebruiken, kunt u ervoor zorgen dat toekomstige wijzigingen in paden uw client code niet beschadigen.

### <a name="working-with-collections-of-entities"></a>Werken met verzamelingen entiteiten

Voorheen heeft de v2 API alle beschik bare entiteiten in een antwoord geretourneerd. Voor een meer nauw keurige controle over de verwachte grootte van het antwoord, in v3 worden alle antwoorden van verzamelingen gepagineerd. U hebt controle over het aantal geretourneerde entiteiten en de verschuiving van de pagina. Dit gedrag maakt het eenvoudig om de runtime van de reactie processor te voors pellen en is consistent met andere Azure-Api's.

De basis vorm van het antwoord is hetzelfde voor alle verzamelingen:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

De eigenschap `values` bevat een subset van de beschik bare verzamelings entiteiten. Het aantal en de offset kunnen worden beheerd met behulp van de query parameters `skip` en `top` . Als `@nextLink` is niet null, zijn er meer gegevens beschikbaar en kan de volgende batch met gegevens worden opgehaald door een Get on-bewerking uit te voeren `$.@nextLink` .

Voor deze wijziging moet het `GET` voor de verzameling in een lus worden aangeroepen totdat alle elementen zijn geretourneerd.

### <a name="creating-transcriptions"></a>Transcripties maken

Een gedetailleerde beschrijving van het maken van transcriptie vindt [u in batch transcriptie How-to](./batch-transcription.md).

Het maken van transcripties is gewijzigd in v3 om expliciet specifieke transcriptie-opties in te stellen. Alle (optioneel) configuratie-eigenschappen kunnen nu worden ingesteld in de `properties` eigenschap.
Versie v3 ondersteunt nu meerdere invoer bestanden en vereist daarom een lijst met Url's en geen enkele URL zoals vereist voor v2. De naam van de eigenschap is gewijzigd van `recordingsUrl` in `contentUrls` . De functionaliteit van het analyseren van sentiment in transcripties is verwijderd op v3. U wordt aangeraden de [tekst analyse](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) van micro soft cognitieve service te gebruiken.

Met de nieuwe eigenschap `timeToLive` onder `properties` kunt u de bestaande voltooide entiteiten weghalen. `timeToLive`Hiermee geeft u de duur op waarna een voltooide entiteit automatisch wordt verwijderd. Stel deze in op een hoge waarde (bijvoorbeeld `PT12H` ) wanneer de entiteiten voortdurend worden getraceerd, verbruikt en verwijderd en daarom doorgaans lang vóór 12 uur worden verwerkt.

v2 transcriptie POST-aanvraag tekst:

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

hoofd tekst van v3 transcriptie POST-aanvraag:

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```

### <a name="format-of-v3-transcription-results"></a>Indeling van v3 transcriptie-resultaten

Het schema van transcriptie-resultaten is enigszins gewijzigd om te worden uitgelijnd met de transcripties die zijn gemaakt door real-time-eind punten. Een uitgebreide beschrijving van de nieuwe indeling vindt u in de [batch-transcriptie How-to](./batch-transcription.md). Het schema van het resultaat wordt gepubliceerd in onze [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples) onder `samples/batch/transcriptionresult_v3.schema.json` .

De eigenschaps namen zijn nu Camel en de waarden voor het kanaal en de spreker maken gebruik van geheeltallige typen. Als u de indeling van de duur wilt uitlijnen met andere Azure-Api's, is deze nu ingedeeld zoals beschreven in ISO 8601.

Voor beeld van een v3-transcriptie resultaat. De verschillen worden beschreven in de opmerkingen.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="getting-the-content-of-entities-and-the-results"></a>De inhoud van entiteiten en de resultaten ophalen

In v2 zijn de koppelingen naar de invoer-of resultaat bestanden genoteerd met de rest van de meta gegevens van de entiteit. Als verbetering van v3 is er een duidelijke schei ding tussen de meta gegevens van de entiteit, die wordt geretourneerd door een GET on `$.self` en de details en referenties voor toegang tot de resultaat bestanden. Deze schei ding helpt de gegevens van klanten te beveiligen en biedt de nauw keurige controle over de geldigheids duur van de referenties.

In v3 is er een eigenschap `files` met de naam onder koppelingen voor het geval de entiteit gegevens beschikbaar stelt (data sets, transcripties, eind punten en evaluaties). Een GET on `$.links.files` retourneert een lijst met bestanden en SAS-URL voor toegang tot de inhoud van elk bestand. Als u de geldigheids duur van de SAS-Url's wilt beheren, kunt u de query parameter `sasValidityInSeconds` gebruiken om de levens duur op te geven.

v2 transcriptie:

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

V3 transcriptie:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

Vervolgens zou een GET on `$.links.files` resulteren in:

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

De `kind` geeft de indeling van de inhoud van het bestand aan. Voor transcripties zijn de bestanden van de soort de `TranscriptionReport` samen vatting van de taak en de bestanden van de soort het `Transcription` resultaat van de taak zelf.

### <a name="customizing-models"></a>Modellen aanpassen

Vóór v3 was er een onderscheid tussen een ' akoestische model ' en een ' taal model ' wanneer een model werd getraind. Dit onderscheid heeft tot gevolg dat er meerdere modellen moeten worden opgegeven bij het maken van eind punten of transcripties. Om dit proces te vereenvoudigen voor een beller, zijn de verschillen verwijderd en zijn alle items afhankelijk van de inhoud van de gegevens sets die worden gebruikt voor model training. Met deze wijziging ondersteunt het maken van het model nu gemengde gegevens sets (taal gegevens en akoestische gegevens). Eind punten en transcripties vereisen nu slechts één model.

Met deze wijziging is de nood zaak van een `kind` in het bericht verwijderd en kunnen er `datasets[]` nu meerdere gegevens sets van dezelfde of gemengde typen worden opgenomen.

Ter verbetering van de resultaten van een getraind model worden de akoestische gegevens automatisch intern gebruikt voor de taal training. Over het algemeen bieden modellen die zijn gemaakt via de V3 API meer nauw keurige resultaten dan modellen die met de v2 API zijn gemaakt.

### <a name="retrieving-base-and-custom-models"></a>Basis-en aangepaste modellen ophalen

Voor het vereenvoudigen van het ophalen van de beschik bare modellen heeft v3 de verzamelingen basis modellen gescheiden van de ' aangepaste modellen ' van de klant. De twee routes zijn nu `GET /speechtotext/v3.0/models/base` en `GET /speechtotext/v3.0/models/` .

Voorheen werden alle modellen samen in één antwoord geretourneerd.

### <a name="name-of-an-entity"></a>Naam van een entiteit

De naam van de eigenschap `name` is gewijzigd in `displayName` . Dit wordt uitgelijnd met andere Azure-Api's om identiteits eigenschappen niet aan te geven. De waarde van deze eigenschap mag niet uniek zijn en kan worden gewijzigd nadat de entiteit is gemaakt met een `PATCH` .

v2 transcriptie:

```json
{
    "name": "Transcription using locale en-US"
}
```

V3 transcriptie:

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>Toegang tot entiteiten waarnaar wordt verwezen

In v2 verwijzingen naar entiteiten waarnaar wordt verwezen, zijn altijd gefactureerd, bijvoorbeeld de gebruikte modellen van een eind punt. Het nesten van entiteiten resulteerde in grote reacties en consumers gebruiken de geneste inhoud zelden. Als u de grootte van het antwoord wilt verkleinen en de prestaties van alle API-gebruikers wilt verbeteren, worden de entiteiten waarnaar wordt verwezen, niet meer in het antwoord in de regel vermeld. In plaats daarvan wordt een verwijzing naar de andere entiteit gebruikt, die rechtstreeks kan worden gebruikt. Deze verwijzing kan worden gebruikt voor een volgende `GET` (ook wel een URL), volgens hetzelfde patroon als de `self` koppeling.

v2 transcriptie:

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
          
        }
      ]
    },
  ]
}
```

V3 transcriptie:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Als u de details van een model waarnaar wordt verwezen wilt gebruiken, zoals wordt weer gegeven in het bovenstaande voor beeld, vereenvoudigt u het probleem van een GET on `$.model.self` .

### <a name="retrieving-endpoint-logs"></a>Eindpunt logboeken ophalen

Versie v2 van de service die de antwoorden van eind punten in de logboek registratie ondersteunt. Om de resultaten van een eind punt met v2 op te halen, moest een ' gegevens export ' worden gemaakt, waarbij een moment opname van de resultaten wordt weer gegeven die zijn gedefinieerd door een tijds bereik. Het proces voor het exporteren van batches met gegevens is inflexibel geworden. De V3 API biedt toegang tot elk afzonderlijk bestand en maakt herhalingen mogelijk.

Een geslaagde v3-eind punt:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

Antwoord van GET `$.links.logs` :

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

De paginering voor eindpunt logboeken werkt op dezelfde manier als alle andere verzamelingen, behalve dat er geen offset kan worden opgegeven. Vanwege de grote hoeveelheid beschik bare gegevens moest de server paginering worden geïmplementeerd.

In v3 kan elk eindpunt logboek afzonderlijk worden verwijderd door een verwijdering uit te geven aan het `self` bestand of door middel van verwijderen op `$.links.logs` . Als u een eind gegevens wilt opgeven, kunt u de query parameter `endDate` toevoegen aan de aanvraag.

### <a name="using-custom-properties"></a>' Aangepaste ' eigenschappen gebruiken

Voor het scheiden van aangepaste eigenschappen van de optionele configuratie-eigenschappen bevinden alle expliciet benoemde eigenschappen zich nu in de `properties` eigenschap en alle eigenschappen die zijn gedefinieerd voor aanroepers bevinden zich nu in de `customProperties` eigenschap.

v2 transcriptie-entiteit

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

V3 transcriptie-entiteit

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Deze wijziging heeft ook het gebruik van de juiste typen voor alle expliciet benoemde eigenschappen onder `properties` (bijvoorbeeld bool in plaats van een teken reeks) ingeschakeld.

### <a name="response-headers"></a>Antwoordheaders

V3 retourneert niet langer de header `Operation-Location` naast de header `Location` op post-aanvragen. De waarde van beide headers gebruikt om precies hetzelfde te zijn. Er wordt nu alleen een `Location` resultaat geretourneerd.

Omdat de nieuwe API-versie nu wordt beheerd door Azure API Management (APIM), worden de gerelateerde headers `X-RateLimit-Limit` beperkt `X-RateLimit-Remaining` en `X-RateLimit-Reset` niet opgenomen in de antwoord headers.

### <a name="accuracy-tests"></a>Nauw keurigheid testen

De naam van nauw keurigheid tests is gewijzigd in evaluaties, omdat de nieuwe naam beter beschrijft wat ze vertegenwoordigen. De nieuws paden zijn bijvoorbeeld ' https://{Region}. API. cognitieve. Microsoft. com/speechtotext/v 3.0/evaluaties '.
