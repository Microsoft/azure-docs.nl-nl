---
title: Migreren van v2 naar v3 REST API-Speech-Service
titleSuffix: Azure Cognitive Services
description: Dit document helpt ontwikkel aars bij het migreren van code van v2 naar v3 in de spraak-naar-tekst REST API speech Services.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c8016b566db8be1b7f5c5ddb8d92123d6673db5
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569841"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>Code migreren van v 2.0 naar v 3.0 van de REST API

Vergeleken met v2 is de V3-versie van de speech Services-REST API voor spraak naar tekst betrouwbaarder, gemakkelijker te gebruiken en consistent met Api's voor soort gelijke Services. De meeste teams kunnen binnen een of twee dagen van v2 naar v3 migreren.

## <a name="forward-compatibility"></a>Compatibiliteit door sturen

Alle entiteiten van v2 kunnen ook worden gevonden in de V3 API onder dezelfde identiteit. Als het schema van een resultaat is gewijzigd, (bijvoorbeeld transcripties), wordt het resultaat van een GET in de V3-versie van de API gebruikt voor het v3-schema. Het resultaat van een GET in de v2-versie van de API maakt gebruik van hetzelfde v2-schema. Nieuw gemaakte entiteiten op v3 zijn **niet**   beschikbaar in reacties van v2-api's. 

## <a name="migration-steps"></a>Migratiestappen

Dit is een overzicht van items waarvan u op de hoogte moet zijn wanneer u de migratie voorbereidt. Meer informatie vindt u in de afzonderlijke koppelingen. Afhankelijk van het huidige gebruik van de API, kunnen niet alle hier vermelde stappen van toepassing zijn. Bij slechts enkele wijzigingen zijn niet-essentiële wijzigingen in de aanroepende code vereist. De meeste wijzigingen vereisen alleen een wijziging in item namen. 

Algemene wijzigingen: 

1. [De hostnaam wijzigen](#host-name-changes)

1. [Wijzig de naam van de eigenschaps-id in Self in uw client code](#identity-of-an-entity) 

1. [Code wijzigen om verzamelingen van entiteiten te herhalen](#working-with-collections-of-entities)

1. [Wijzig de naam van de eigenschap in displayName in de client code](#name-of-an-entity)

1. [Het ophalen van de meta gegevens van entiteiten waarnaar wordt verwezen, aanpassen](#accessing-referenced-entities)

1. Als u batch-transcriptie gebruikt: 

    * [Code voor het maken van batch-transcripties aanpassen](#creating-transcriptions) 

    * [Code aan het nieuwe transcriptie-resultaten schema aanpassen](#format-of-v3-transcription-results)

    * [Code voor het ophalen van resultaten aanpassen](#getting-the-content-of-entities-and-the-results)

1. Als u aangepaste model training/test-Api's gebruikt: 

    * [Wijzigingen Toep assen op aangepaste model training](#customizing-models)

    * [Wijzigen hoe basis-en aangepaste modellen worden opgehaald](#retrieving-base-and-custom-models)

    * [De naam van het pad segment accuracytests wijzigen in evaluaties in uw client code](#accuracy-tests)

1. Als u endpoints-Api's gebruikt:

    * [Wijzigen hoe eindpunt logboeken worden opgehaald](#retrieving-endpoint-logs)

1. Andere kleine wijzigingen: 

    * [Alle aangepaste eigenschappen door geven als customProperties in plaats van eigenschappen in uw POST-aanvragen](#using-custom-properties)

    * [De locatie van de locatie van de antwoord tekst lezen in plaats van de locatie van de bewerking](#response-headers)

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

### <a name="host-name-changes"></a>Gewijzigde namen van hosts

De hostnaam van het eind punt is gewijzigd van `{region}.cris.ai` in `{region}.api.cognitive.microsoft.com` . Paden naar de nieuwe eind punten bevatten niet meer `api/` , omdat deze deel uitmaakt van de hostnaam. In het [Swagger-document](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) worden geldige regio's en paden weer gegeven.
>[!IMPORTANT]
>Wijzig de hostnaam in `{region}.cris.ai` `{region}.api.cognitive.microsoft.com` de regio van uw spraak abonnement. Verwijder ook `api/` uit elk pad in uw client code.

### <a name="identity-of-an-entity"></a>Identiteit van een entiteit

De eigenschap `id` is nu `self` . In v2 moest een API-gebruiker weten hoe onze paden op de API worden gemaakt. Dit was niet-uitbreidbaar en vereist overbodig werk van de gebruiker. De eigenschap `id` (UUID) wordt vervangen door `self` (teken reeks), de locatie van de entiteit (URL). De waarde is nog uniek tussen alle entiteiten. Als een `id` teken reeks in uw code is opgeslagen, is een naamswijziging voldoende om het nieuwe schema te ondersteunen. U kunt nu de `self` inhoud gebruiken als de URL voor de `GET` -, `PATCH` -en `DELETE` rest-aanroepen voor uw entiteit.

Als de entiteit extra functionaliteit beschikbaar heeft via andere paden, worden deze weer gegeven onder `links` . In het volgende voor beeld voor transcriptie wordt een afzonderlijke methode voor `GET` de inhoud van de transcriptie weer gegeven:
>[!IMPORTANT]
>Wijzig de naam van de eigenschap `id` `self` in in de client code. Wijzig het type van `uuid` in `string` indien nodig. 

**v2 transcriptie:**

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

**V3 transcriptie:**

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

Afhankelijk van de implementatie van uw code is het mogelijk niet voldoende om de naam van de eigenschap te wijzigen. We raden u aan de geretourneerde `self` en `links` waarden als doel-url's van uw rest-aanroepen te gebruiken in plaats van paden in uw client te genereren. Door de geretourneerde Url's te gebruiken, kunt u ervoor zorgen dat toekomstige wijzigingen in paden uw client code niet beschadigen.

### <a name="working-with-collections-of-entities"></a>Werken met verzamelingen entiteiten

Voorheen hebben de v2 API alle beschik bare entiteiten geretourneerd. Om een meer nauw keurige controle over de verwachte grootte van het antwoord in v3 toe te staan, worden alle verzamelings resultaten gepagineerd. U hebt controle over het aantal geretourneerde entiteiten en de begin verschuiving van de pagina. Dit gedrag maakt het eenvoudig om de runtime van de reactie processor te voors pellen.

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

De `values` eigenschap bevat een subset van de beschik bare verzamelings entiteiten. Het aantal en de offset kunnen worden beheerd met behulp van de- `skip` en- `top` query parameters. Als dat `@nextLink` niet het `null` geval is, zijn er meer gegevens beschikbaar en kan de volgende batch met gegevens worden opgehaald door een Get on-bewerking uit te voeren `$.@nextLink` .

Voor deze wijziging moet het `GET` voor de verzameling in een lus worden aangeroepen totdat alle elementen zijn geretourneerd.

>[!IMPORTANT]
>Wanneer het antwoord op een GET to `speechtotext/v3.0/{collection}` bevat een waarde in `$.@nextLink` , blijft de uitgave `GETs` op `$.@nextLink` until `$.@nextLink` niet ingesteld op het ophalen van alle elementen van die verzameling.

### <a name="creating-transcriptions"></a>Transcripties maken

Een gedetailleerde beschrijving van het maken van batches met transcripties vindt u in [batch transcriptie How-to](./batch-transcription.md).

Met de V3 transcriptie-API kunt u specifieke transcriptie-opties expliciet instellen. Alle (optioneel) configuratie-eigenschappen kunnen nu worden ingesteld in de `properties` eigenschap.
Versie v3 ondersteunt ook meerdere invoer bestanden, zodat er een lijst met Url's is vereist in plaats van een enkele URL als v2. De naam van de eigenschap v2 `recordingsUrl` is nu `contentUrls` in v3. De functionaliteit van het analyseren van sentiment in transcripties is verwijderd in v3. Zie [tekst analyse](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) van micro soft cognitieve service voor sentiment-analyse opties.

Met de nieuwe eigenschap `timeToLive` onder `properties` kunt u de bestaande voltooide entiteiten weghalen. `timeToLive`Hiermee geeft u de duur op waarna een voltooide entiteit automatisch wordt verwijderd. Stel deze in op een hoge waarde (bijvoorbeeld `PT12H` ) wanneer de entiteiten voortdurend worden bijgehouden, verbruikt en verwijderd en daarom doorgaans lang vóór 12 uur zijn verstreken.

**v2 transcriptie POST-aanvraag tekst:**

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

**hoofd tekst van v3 transcriptie POST-aanvraag:**

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
>[!IMPORTANT]
>Wijzig de naam van de eigenschap `recordingsUrl` in `contentUrls` en geef een matrix met url's door in plaats van een enkele URL. Geef instellingen voor `diarizationEnabled` of `wordLevelTimestampsEnabled` op `bool` in plaats van `string` .

### <a name="format-of-v3-transcription-results"></a>Indeling van v3 transcriptie-resultaten

Het schema van transcriptie-resultaten is enigszins gewijzigd om te worden uitgelijnd met transcripties die zijn gemaakt door real-time-eind punten. Zoek een uitgebreide beschrijving van de nieuwe indeling in de [batch-transcriptie](./batch-transcription.md). Het schema van het resultaat wordt gepubliceerd in onze [github-voorbeeld opslagplaats](https://aka.ms/csspeech/samples) onder `samples/batch/transcriptionresult_v3.schema.json` .

Eigenschaps namen zijn nu Camel en de waarden voor `channel` en `speaker` gebruiken nu gehele getallen. Voor de duur van de tijd wordt nu de structuur gebruikt die wordt beschreven in ISO 8601, die overeenkomt met de duur notatie die wordt gebruikt in andere Azure-Api's.

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
>[!IMPORTANT]
>Deserialisatie van het transcriptie-resultaat in het nieuwe type zoals hierboven wordt weer gegeven. In plaats van één bestand per audio kanaal onderscheidt u kanalen door de waarde van de eigenschap `channel` voor elk-element in te controleren `recognizedPhrases` . Er is nu één resultaat bestand voor elk invoer bestand.


### <a name="getting-the-content-of-entities-and-the-results"></a>De inhoud van entiteiten en de resultaten ophalen

In v2 zijn de koppelingen naar de invoer-of resultaat bestanden genoteerd met de rest van de meta gegevens van de entiteit. Als verbetering van v3 is er een duidelijke schei ding tussen de meta gegevens van de entiteit (die worden geretourneerd door een GET aan `$.self` ) en de details en referenties voor toegang tot de resultaat bestanden. Met deze schei ding worden klant gegevens beschermd en kunnen ze de duur van de geldigheid van de referenties nauw keurig bepalen.

In v3 `links` neemt u een sub-eigenschap `files` op die wordt aangeroepen als de entiteit gegevens (gegevens sets, transcripties, eind punten of evaluaties) beschikbaar stelt. Een GET on `$.links.files` retourneert een lijst met bestanden en een SAS-URL voor toegang tot de inhoud van elk bestand. Als u de geldigheids duur van de SAS-Url's wilt beheren, kunt u de query parameter `sasValidityInSeconds` gebruiken om de levens duur op te geven.

**v2 transcriptie:**

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

**V3 transcriptie:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**Een GET on `$.links.files` zou resulteren in:**

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

De `kind` eigenschap geeft de indeling van de inhoud van het bestand aan. Voor transcripties zijn de bestanden van de soort de `TranscriptionReport` samen vatting van de taak en de bestanden van de soort het `Transcription` resultaat van de taak zelf.

>[!IMPORTANT]
>Als u de resultaten van de bewerkingen wilt ophalen, gebruikt u a `GET` op `/speechtotext/v3.0/{collection}/{id}/files` . deze zijn niet langer opgenomen in de reacties van `GET` op `/speechtotext/v3.0/{collection}/{id}` of `/speechtotext/v3.0/{collection}` .

### <a name="customizing-models"></a>Modellen aanpassen

Vóór v3 was er een verschil tussen een _akoestisch model_ en een _taal model_ toen een model werd getraind. Dit onderscheid heeft tot gevolg dat er meerdere modellen moeten worden opgegeven bij het maken van eind punten of transcripties. Om dit proces te vereenvoudigen voor een beller, zijn de verschillen verwijderd en zijn alle items afhankelijk van de inhoud van de gegevens sets die worden gebruikt voor model training. Met deze wijziging ondersteunt het maken van het model nu gemengde gegevens sets (taal gegevens en akoestische gegevens). Eind punten en transcripties vereisen nu slechts één model.

Met deze wijziging is de nood zaak van een `kind` in de `POST` bewerking verwijderd en kan de `datasets[]` matrix nu meerdere gegevens sets van dezelfde of gemengde typen bevatten.

Ter verbetering van de resultaten van een getraind model worden de akoestische gegevens automatisch intern gebruikt tijdens de taal training. Over het algemeen bieden modellen die zijn gemaakt via de V3 API meer nauw keurige resultaten dan modellen die met de v2 API zijn gemaakt.

>[!IMPORTANT]
>Als u zowel het akoestische als het taal model onderdeel wilt aanpassen, geeft u alle vereiste taal en akoestische gegevens sets in `datasets[]` het bericht door `/speechtotext/v3.0/models` . Hiermee maakt u één model waarin beide onderdelen zijn aangepast.

### <a name="retrieving-base-and-custom-models"></a>Basis-en aangepaste modellen ophalen

Voor het vereenvoudigen van het ophalen van de beschik bare modellen heeft v3 de verzamelingen basis modellen gescheiden van de ' aangepaste modellen ' van de klant. De twee routes zijn nu `GET /speechtotext/v3.0/models/base` en `GET /speechtotext/v3.0/models/` .

In v2 werden alle modellen samen in één antwoord geretourneerd.

>[!IMPORTANT]
>Als u een lijst met de meegeleverde basis modellen voor aanpassing wilt ophalen, gebruikt u `GET` op `/speechtotext/v3.0/models/base` . U kunt uw eigen aangepaste modellen vinden met een `GET` on `/speechtotext/v3.0/models` .

### <a name="name-of-an-entity"></a>Naam van een entiteit

De `name` eigenschap is nu `displayName` . Dit is consistent met andere Azure-Api's om identiteits eigenschappen niet aan te geven. De waarde van deze eigenschap mag niet uniek zijn en kan worden gewijzigd nadat de entiteit is gemaakt met een `PATCH` bewerking.

**v2 transcriptie:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**V3 transcriptie:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>Wijzig de naam van de eigenschap `name` `displayName` in in de client code.

### <a name="accessing-referenced-entities"></a>Toegang tot entiteiten waarnaar wordt verwezen

In v2 werden entiteiten waarnaar wordt verwezen, altijd in het overzicht beschreven, bijvoorbeeld de gebruikte modellen van een eind punt. Het nesten van entiteiten resulteerde in grote reacties en consumers gebruiken de geneste inhoud zelden. Als u de grootte van het antwoord wilt verkleinen en de prestaties wilt verbeteren, worden de entiteiten waarnaar wordt verwezen, niet meer in het antwoord vermeld. In plaats daarvan wordt een verwijzing naar de andere entiteit weer gegeven en kan deze direct worden gebruikt voor een volgende `GET` (ook wel een URL), volgens hetzelfde patroon als de `self` koppeling.

**v2 transcriptie:**

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

**V3 transcriptie:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Als u de details van een model waarnaar wordt verwezen wilt gebruiken, zoals wordt weer gegeven in het bovenstaande voor beeld, hoeft u alleen maar een GET on aan te geven `$.model.self` .

>[!IMPORTANT]
>Voor het ophalen van de meta gegevens van entiteiten waarnaar wordt verwezen, geeft u een GET aan `$.{referencedEntity}.self` , bijvoorbeeld om het model van een transcriptie op te halen `GET` `$.model.self` .


### <a name="retrieving-endpoint-logs"></a>Eindpunt logboeken ophalen

Versie v2 van de service ondersteunt het eind punt resultaat van de logboek registratie. Als u de resultaten van een eind punt met v2 wilt ophalen, maakt u een ' gegevens export ', waarin een moment opname wordt weer gegeven van de resultaten die zijn gedefinieerd door een tijds bereik. Het proces voor het exporteren van batches met gegevens was inflexibeler. De V3 API biedt toegang tot elk afzonderlijk bestand en maakt herhalingen mogelijk.

**Een geslaagde v3-eind punt:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**Antwoord van GET `$.links.logs` :**

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

De paginering voor eindpunt logboeken werkt op dezelfde manier als alle andere verzamelingen, behalve dat er geen offset kan worden opgegeven. Vanwege de grote hoeveelheid beschik bare gegevens, wordt de paginering bepaald door de-server.

In v3 kan elk eindpunt logboek afzonderlijk worden verwijderd door een bewerking uit te geven `DELETE` voor het `self` bestand of door gebruik te maken `DELETE` van `$.links.logs` . Als u een eind datum wilt opgeven, kunt u de query parameter `endDate` toevoegen aan de aanvraag.

>[!IMPORTANT]
>U hoeft geen logboeken te maken bij het `/api/speechtotext/v2.0/endpoints/{id}/data` Gebruik `/v3.0/endpoints/{id}/files/logs/` om afzonderlijke logboek bestanden te openen. 

### <a name="using-custom-properties"></a>Aangepaste eigenschappen gebruiken

Voor het scheiden van aangepaste eigenschappen van de optionele configuratie-eigenschappen bevinden alle expliciet benoemde eigenschappen zich nu in de `properties` eigenschap en alle eigenschappen die door de aanroepers zijn gedefinieerd, bevinden zich nu in de `customProperties` eigenschap.

**v2 transcriptie-entiteit:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**V3 transcriptie-entiteit:**

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

Met deze wijziging kunt u ook de juiste typen gebruiken voor alle expliciet benoemde eigenschappen onder `properties` (bijvoorbeeld Booleaans in plaats van een teken reeks).

>[!IMPORTANT]
>Geef alle aangepaste eigenschappen door in `customProperties` plaats van `properties` in uw `POST` aanvragen.

### <a name="response-headers"></a>Antwoordheaders

V3 retourneert niet langer de `Operation-Location` header naast de `Location` koptekst op `POST` aanvragen. De waarde van beide headers in v2 is hetzelfde. Nu `Location` wordt alleen geretourneerd.

Omdat de nieuwe API-versie nu wordt beheerd door Azure API Management (APIM), worden de gerelateerde headers `X-RateLimit-Limit` beperkt `X-RateLimit-Remaining` en `X-RateLimit-Reset` niet opgenomen in de antwoord headers.

>[!IMPORTANT]
>Lees de locatie van de antwoord header `Location` in plaats van `Operation-Location` . In het geval van een 429-respons code leest u de `Retry-After` waarde header in plaats van `X-RateLimit-Limit` , `X-RateLimit-Remaining` of `X-RateLimit-Reset` .


### <a name="accuracy-tests"></a>Nauw keurigheid testen

De naam van nauw keurigheid tests is gewijzigd in evaluaties, omdat de nieuwe naam beter beschrijft wat ze vertegenwoordigen. De nieuwe paden zijn: `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations` .

>[!IMPORTANT]
>Wijzig de naam van het padsegment `accuracytests` `evaluations` in uw client code.


## <a name="next-steps"></a>Volgende stappen

Bekijk alle functies van deze veelgebruikte REST Api's die worden meegeleverd met spraak Services:

* [REST API voor spraak-naar-tekst](rest-speech-to-text.md)
* [Swagger-document](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) voor v3 van de rest API
* Bekijk de voorbeeld [opslagplaats voor github](https://aka.ms/csspeech/samples) in de submap voor voorbeeld code voor het uitvoeren van batch transcripties `samples/batch` .
