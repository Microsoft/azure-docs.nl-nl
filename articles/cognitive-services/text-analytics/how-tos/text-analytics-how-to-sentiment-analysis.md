---
title: Sentimentanalyse uitvoeren met Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u sentiment in tekst kunt detecteren met de Azure Cognitive Services Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 11/11/2020
ms.author: aahi
ms.openlocfilehash: 87e6ad488438ae28467f6e904fbb57f7ca5448ff
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518172"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>Procedure: Sentiment detecteren met behulp van de Text Analytics API

De functie Sentimentanalyse van de Text Analytics API evalueert tekst en retourneert sentimentscores en -labels voor elke zin. Deze functie is handig voor het detecteren van positief en negatief sentiment in sociale media, klantbeoordelingen, discussieforums en meer. De AI-modellen die door de API worden gebruikt, worden door de service verschaft. U hoeft alleen maar inhoud voor analyse te verzenden.

Na verzending van een aanvraag voor sentimentanalyse retourneert de API sentimentlabels (zoals 'negatief', 'neutraal' en 'positief') en betrouwbaarheidsscores op zins- en documentniveau.

Sentimentanalyse ondersteunt een breed scala aan talen, met meer in preview-versie. Zie voor meer informatie [Ondersteunde talen](../language-support.md).

## <a name="sentiment-analysis-versions-and-features"></a>Versies en functies van Sentimentanalyse

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Functie                                   | Sentimentanalyse v3 | Sentimentanalyse v3.1 (preview) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Methoden voor afzonderlijke aanvragen en batchaanvragen    | X                     | X                                 |
| Sentimentscores en -labels             | X                     | X                                 |
| [Docker-container](text-analytics-how-to-install-containers.md) op basis van Linux | X  |  |
| Meninganalyse                            |                       | X                                 |

## <a name="sentiment-scoring-and-labeling"></a>Sentimentscores en -labels

In Sentimentanalyse v3 worden sentimentlabels toegepast op tekst, die worden geretourneerd op zins- en documentniveau. Elk label heeft ook een betrouwbaarheidsscore. 

De labels zijn *positief*, *negatief* en *neutraal*. Op documentniveau kan ook het *gemengde* sentimentlabel worden geretourneerd. Het sentiment van het document wordt hieronder bepaald:

| Sentiment in zin                                                                            | Geretourneerd documentlabel |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Het document bevat ten minste één zin die `positive` is. De overige zinnen zijn `neutral`. | `positive`              |
| Het document bevat ten minste één zin die `negative` is. De overige zinnen zijn `neutral`. | `negative`              |
| Het document bevat ten minste één `negative` zin en ten minste één `positive` zin.    | `mixed`                 |
| Alle zinnen in het document zijn `neutral`.                                                  | `neutral`               |

Betrouwbaarheidsscores kunnen variëren van 1 tot 0. Scores die dichter bij 1 liggen, geven een hogere betrouwbaarheid in de classificatie van het label aan. Lagere scores geven een lagere betrouwbaarheid aan. Voor elk document of elke zin zijn de voorspelde scores behorende bij de labels (positief, negatief en neutraal) opgeteld 1.

## <a name="opinion-mining"></a>Meninganalyse

Meninganalyse is een functie van Sentimentanalyse vanaf versie 3.1-preview.1. Deze functie wordt ook wel op aspecten gebaseerde sentimentanalyse in natuurlijke taalverwerking (NLP) en biedt gedetailleerde informatie over de meningen over bepaalde aspecten (zoals de kenmerken van producten of diensten) in de tekst.

Als een klant bijvoorbeeld als feedback over een hotel 'de kamer was geweldig, maar het personeel was onvriendelijk' geeft, worden in de meninganalyse aspecten van de tekst en de bijbehorende meningen en sentimenten gevonden:

| Aspect | Mening    | Sentiment |
|--------|------------|-----------|
| ruimte   | geweldig      | positief  |
| personeel  | onvriendelijk | negatief  |

Voor een meninganalyse moet u de vlag `opinionMining=true` opnemen in een sentimentanalyse-aanvraag. De resultaten van de meninganalyse worden opgenomen in het antwoord van de sentimentanalyse.

## <a name="sending-a-rest-api-request"></a>Een REST API-aanvraag verzenden 

### <a name="preparation"></a>Voorbereiding

Sentimentanalyses produceren een hoger kwaliteitsresultaat wanneer u ze kleinere hoeveelheden tekst aanbiedt om op te werken. Dit is het tegenovergestelde van sleuteltermextractie, wat beter presteert op grotere blokken tekst. Voor de beste resultaten bij beide activiteiten, zou u de invoeren dienovereenkomstig kunnen herstructureren.

U moet JSON-documenten in deze indeling hebben: id, tekst en taal.

De documenten mogen niet meer dan 5.120 tekens per document bevatten. Een verzameling mag maximaal 1000 items (id's) bevatten. De verzameling is in de hoofdtekst van de aanvraag ingediend.

## <a name="structure-the-request"></a>Structureer de aanvraag

Maak een POST-aanvraag. Gebruik [Postman](text-analytics-how-to-call-api.md) of de **API-testconsole** in de volgende referentiekoppelingen om snel een aanvraag te structureren en verzenden. 

#### <a name="version-31-preview2"></a>[Versie 3.1-preview.2](#tab/version-3-1)

[Referentie voor Sentimentanalyse v3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-2/operations/Sentiment)

#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)

[Referentie voor Sentimentanalyse v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>Eindpunten voor aanvragen

Stel het HTTPS-eindpunt voor sentimentanalyse in met behulp van een Text Analytics-resource in Azure of een geïnstantieerde [Text Analytics-container](text-analytics-how-to-install-containers.md). In de aanvraag moet u de juiste URL opnemen voor de versie die u wilt gebruiken. Bijvoorbeeld:

> [!NOTE]
> U vindt de sleutel en het eindpunt voor uw Text Analytics-resource in Azure Portal. U vindt deze op de **Quickstart**-pagina van de resource, onder **Resourcebeheer**. 

#### <a name="version-31-preview2"></a>[Versie 3.1-preview.2](#tab/version-3-1)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/sentiment`

Als u meninganalyseresultaten wenst, moet u de parameter `opinionMining=true` toevoegen. Bijvoorbeeld:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/sentiment?opinionMining=true`

Deze parameter is standaard ingesteld op `false`. 

#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

Stel een hoofdtekst in om uw Text Analytics-API-sleutel in de aanvraag op te nemen. Verstrek in de hoofdtekst van de aanvraag de JSON-documentenverzameling die u hebt voorbereid voor deze analyse.

### <a name="example-sentiment-analysis-request"></a>Voorbeeld van Sentimentanalyse-aanvraag 

Hier volgt een voorbeeld van de inhoud die u voor gevoelsanalyse kan indienen. De indeling van de aanvraag is hetzelfde voor beide versies.
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>Plaats de aanvraag

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie over [gegevenslimieten](../overview.md#data-limits) in het overzicht voor informatie over de grootte en het aantal aanvragen dat u per minuut en per seconde kunt verzenden.

De Text Analytics-API is stateless (staatloos). Er worden geen gegevens in uw account opgeslagen en de resultaten worden onmiddellijk in het antwoord geretourneerd.


### <a name="view-the-results"></a>De resultaten bekijken

Sentimentanalyse retourneert een sentimentlabel en een betrouwbaarheidsscore voor het hele document, en elke zin daarin. Scores die dichter bij 1 liggen, geven een hogere betrouwbaarheid in de classificatie van het label aan. Lagere scores geven een lagere betrouwbaarheid aan. Een document kan meerdere zinnen bevatten en het totaal van alle betrouwbaarheidsscores binnen elk document of zin is 1.

Uitvoer wordt onmiddellijk geretourneerd. U kunt de resultaten streamen naar een toepassing die JSON accepteert of de uitvoer op het lokale systeem opslaan als een bestand. Vervolgens importeert u de uitvoerput in een toepassing waarin u de gegevens kunt sorteren, doorzoeken en bewerken. Vanwege meertalige en Emoji-ondersteuning kan het antwoord tekstverschuivingen bevatten. Zie [Verschuivingen verwerken](../concepts/text-offsets.md) voor meer informatie.

#### <a name="version-31-preview2"></a>[Versie 3.1-preview.2](#tab/version-3-1)

### <a name="sentiment-analysis-v31-example-response"></a>Voorbeeld van antwoord van Sentimentanalyse v3.1

Sentimentanalyse v3.1 biedt naast het antwoordobject op het tabblad **Versie 3.0** ook een meninganalyse. In het onderstaande antwoord heeft de zin *Het restaurant had heerlijk eten en de ober was vriendelijk* twee aspecten: *eten* en *ober*. De eigenschap `relations` van elk aspect bevat een `ref`-waarde met de URI-referentie naar de gekoppelde `documents`-, `sentences`- en `opinions`-objecten.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)

### <a name="sentiment-analysis-v30-example-response"></a>Voorbeeld van antwoord van Sentimentanalyse v 3.0

Antwoorden van Sentimentanalyse v3 bevatten sentimentlabels en -scores voor elke geanalyseerde zin en elk geanalyseerd document.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en de werkstroom geleerd voor sentimentanalyse met behulp van de Text Analytics-API. Samenvatting:

+ Sentimentanalyse is beschikbaar voor de geselecteerde talen.
+ JSON-documenten in de aanvraagbody omvatten een id, tekst en taalcode.
+ De POST-aanvraag wordt verzonden naar een `/sentiment`-eindpunt met behulp van een persoonlijke [toegangssleutel en een eindpunt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) die geldig zijn voor uw abonnement.
+ Antwoorduitvoer, die uit een sentimentscore voor elke document-id bestaat, kan worden gestreamd naar alle apps die JSON accepteren. Excel en Power BI, bijvoorbeeld.

## <a name="see-also"></a>Zie ook

* [Overzicht van Text Analytics](../overview.md)
* [De Text Analytics-clientbibliotheek gebruiken](../quickstarts/text-analytics-sdk.md)
* [Nieuwe functies](../whats-new.md)