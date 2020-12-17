---
title: Extractie van sleutel zinnen met behulp van de Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: Sleutel zinnen extra heren met behulp van de REST API Text Analytics van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: 91e10c25d2c3bef9c1ca20e3e5737a326d45997c
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654775"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Voor beeld: sleutel zinnen extra heren met behulp van Text Analytics

De [Sleuteltermextractie API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) evalueert ongestructureerde tekst en retourneert voor elk JSON-document een lijst met sleuteltermen.

Deze functie is handig als u snel de belangrijkste punten moet identificeren van een verzameling documenten. Bijvoorbeeld, bij de invoertekst "het eten was heerlijk en de bediening fantastisch", retourneert de service de belangrijkste gespreksonderwerpen: 'eten' en "bediening fantastisch".

Zie voor meer informatie [Ondersteunde talen](../language-support.md).

> [!TIP]
> * Text Analytics biedt ook een Docker-containerinstallatiekopie op basis van Linux voor sleuteltermextractie. U kunt de [Text Analytics-container dus dicht bij uw gegevens installeren en uitvoeren](text-analytics-how-to-install-containers.md).
> * U kunt deze functie ook [asynchroon](text-analytics-how-to-call-api.md) gebruiken met behulp van het `/analyze` eind punt.

## <a name="preparation"></a>Voorbereiding

Sleuteltermextractie werkt het beste wanneer u grotere segmenten tekst opgeeft. Dit is het tegenovergestelde van sentimentanalyse, wat beter presteert op kleinere blokken tekst. Voor de beste resultaten bij beide activiteiten, zou u de invoeren dienovereenkomstig kunnen herstructureren.

U moet JSON-documenten hebben met de volgende indeling: ID, tekst, taal

De document grootte moet 5.120 of minder tekens per document zijn en u kunt Maxi maal 1.000 items (Id's) per verzameling hebben. De verzameling is in de hoofdtekst van de aanvraag ingediend. Het volgende voorbeeld geeft de inhoud die u kunt indienen voor sleuteltermextractie. 

Zie [de Text Analytics-API aanroepen](text-analytics-how-to-call-api.md) voor meer informatie over aanvraag-en reactie objecten.  

### <a name="example-synchronous-request-object"></a>Voor beeld van een synchrone aanvraag object


```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

### <a name="example-asynchronous-request-object"></a>Voor beeld van een asynchroon aanvraag object

Vanaf `v3.1-preview.3` kunt u ner aanvragen asynchroon verzenden met behulp van het `/analyze` eind punt.


```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
    }
}
```

## <a name="step-1-structure-the-request"></a>Stap 1: Structuur van de aanvraag

Zie [de Text Analytics-API aanroepen](text-analytics-how-to-call-api.md)voor informatie over de definitie van aanvragen. De volgende punten zijn voor uw gemak opnieuw geformuleerd:

+ Maak een **post** -aanvraag. Raadpleeg de API-documentatie voor deze aanvraag: [Key frases-API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases).

+ Stel het HTTP-eind punt voor de extractie van sleutel woordgroepen in met behulp van een Text Analytics bron in azure of een geïnstantieerd [Text Analytics container](text-analytics-how-to-install-containers.md). Als u de API synchroon gebruikt, moet u `/text/analytics/v3.0/keyPhrases` in de URL toevoegen. Bijvoorbeeld: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`.

+ Stel een aanvraagheader in om de [toegangssleutel](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) voor de Text Analytics-bewerkingen op te nemen.

+ Verstrek in de hoofdtekst van de aanvraag de JSON-documentenverzameling die u hebt voorbereid voor deze analyse.

> [!Tip]
> Gebruik [Postman](text-analytics-how-to-call-api.md) of open de **API-testconsole** in de [documentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) om de aanvraag te structureren en POST deze in de service.

## <a name="step-2-post-the-request"></a>Stap 2: De aanvraag posten

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie [gegevens limieten](../overview.md#data-limits) in het overzicht voor meer informatie over de grootte en het aantal aanvragen dat u per minuut of per seconde kunt verzenden.

Terughalen als de service staatloos is. Er worden geen gegevens opgeslagen in uw account. Resultaten worden onmiddellijk in het antwoord geretourneerd.

## <a name="step-3-view-results"></a>Stap 3: Resultaten weergeven

Alle POST-verzoeken retourneren een ingedeeld JSON-antwoord met de id's en gedetecteerde eigenschappen. De volg orde van de geretourneerde sleutel zinnen wordt intern bepaald door het model.

Uitvoer wordt onmiddellijk geretourneerd. U kunt de resultaten streamen naar een toepassing die JSON accepteert of u kunt de uitvoer opslaan als lokaal bestand en vervolgens importeren in een toepassing waarmee u kunt sorteren, zoeken en de gegevens kunt manipuleren.

Hier ziet u een voor beeld van de uitvoer voor het uitpakken van de sleutel woordgroep van de v 3.1-Preview. 2-eind punt:

### <a name="synchronous-result"></a>Synchroon resultaat

```json
    {
       "documents":[
          {
             "id":"1",
             "keyPhrases":[
                "year",
                "trail",
                "trip",
                "views",
                "hike"
             ],
             "warnings":[]
          },
          {
             "id":"2",
             "keyPhrases":[
                "marked trails",
                "Worst hike",
                "goners"
             ],
             "warnings":[]
          },
          {
             "id":"3",
             "keyPhrases":[
                "trail",
                "small children",
                "family"
             ],
             "warnings":[]
          },
          {
             "id":"4",
             "keyPhrases":[
                "spectacular views",
                "trail",
                "Worth",
                "area"
             ],
             "warnings":[]
          },
          {
             "id":"5",
             "keyPhrases":[
                "places",
                "beautiful views",
                "favorite trail",
                "rest"
             ],
             "warnings":[]
          }
       ],
       "errors":[],
       "modelVersion":"2020-07-01"
    }

```
Zoals aangegeven, detecteert en negeert het analyse programma niet-essentiële woorden en worden enkele termen of zinsdelen weer gegeven die het onderwerp of object van een zin lijken te zijn.

### <a name="asynchronous-result"></a>Asynchroon resultaat

Als u het `/analyze` eind punt gebruikt voor asynchrone bewerking, ontvangt u een antwoord met de taken die u naar de API hebt verzonden.

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd voor het uitpakken van sleutel zinnen door gebruik te maken van Text Analytics in Cognitive Services. Samenvatting:

+ [Sleuteltermextractie-API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) is beschikbaar in bepaalde talen.
+ JSON-documenten in de aanvraagbody omvatten een id, tekst en taalcode.
+ POST-aanvraag is naar `/keyphrases` een `/analyze` -of-eind punt met behulp van een aangepaste [toegangs sleutel en een eind punt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dat geldig is voor uw abonnement.
+ De uitvoer van antwoorden, die bestaat uit sleutel woorden en zinsdelen voor elke document-ID, kan worden gestreamd naar elke app die JSON accepteert, met inbegrip van Microsoft Office Excel en Power BI, om een paar te noemen.

## <a name="see-also"></a>Zie ook

 [Overzicht van Text Analytics](../overview.md) [Veelgestelde vragen](../text-analytics-resource-faq.md)</br>
 [Text Analytics-productpagina](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Text Analytics](../overview.md)
* [De Text Analytics-clientbibliotheek gebruiken](../quickstarts/client-libraries-rest-api.md)
* [Nieuwe functies](../whats-new.md)
