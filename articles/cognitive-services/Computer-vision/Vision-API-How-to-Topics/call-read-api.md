---
title: De Lees-API aanroepen
titleSuffix: Azure Cognitive Services
description: Meer informatie over het aanroepen van de Lees-API en het configureren van het gedrag.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: 8e0ef789653181d744100ef6e179bcf328f6d704
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308622"
---
# <a name="call-the-read-api"></a>De Read-API aanroepen

In deze hand leiding leert u hoe u de Lees-API aanroept om tekst uit afbeeldingen te halen. U leert de verschillende manieren waarop u het gedrag van deze API kunt configureren om te voldoen aan uw behoeften.

In deze hand leiding wordt ervan uitgegaan dat u al <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" een computer vision resource hebt gemaakt om "  target="_blank"> een computer vision resource </a> te maken en om een abonnements sleutel en eind punt-URL te verkrijgen. Als u dat nog niet hebt gedaan, volgt u een [Snelstartgids](../quickstarts-sdk/client-library.md) om aan de slag te gaan.

## <a name="submit-data-to-the-service"></a>Gegevens verzenden naar de service

De [Lees aanroep](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) van de Lees-API neemt een afbeeldings-of PDF-document als invoer en extraheert tekst asynchroon.

`https://{endpoint}/vision/v3.2/read/analyze[?language][&pages][&readingOrder]`

De aanroep retourneert een veld met een antwoord header met de naam `Operation-Location` . De `Operation-Location` waarde is een URL die de bewerkings-id bevat die in de volgende stap moet worden gebruikt.

|Reactie header| Voorbeeldwaarde |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.2/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Facturering** 
>
> De pagina met [Computer Vision prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) bevat de prijs categorie voor lezen. Elke geanalyseerde afbeelding of pagina is één trans actie. Als u de bewerking aanroept met een PDF-of TIFF-document met 100 pagina's, telt de Lees bewerking deze als 100-trans acties en wordt er voor 100 trans acties gefactureerd. Als u 50-aanroepen hebt gedaan voor de bewerking en elke oproep een document met 100 pagina's heeft verzonden, wordt u gefactureerd voor 50 X 100 = 5000 trans acties.

## <a name="determine-how-to-process-the-data"></a>Bepalen hoe de gegevens moeten worden verwerkt

### <a name="language-specification"></a>Taal specificatie

De [Lees](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) aanroep heeft een optionele aanvraag parameter voor een taal. Lees ondersteuning voor automatische taal identificatie en meertalige documenten, zodat u alleen een taal code kunt opgeven als u wilt afdwingen dat het document wordt verwerkt als die specifieke taal.

### <a name="natural-reading-order-output-latin-languages-only"></a>Uitvoer van natuurlijke Lees volgorde (alleen Latijnse talen)
Geef de volg orde op waarin de tekst regels worden uitgevoerd met de `readingOrder` query parameter. Gebruik `natural` dit voor een meer mensen vriendelijke Lees volgorde, zoals wordt weer gegeven in het volgende voor beeld. Deze functie wordt alleen ondersteund voor Latijnse talen.

:::image border type="content" source="../Images/ocr-reading-order-example.png" alt-text="Voor beeld van OCR-Lees volgorde":::



### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Pagina ('s) of paginabereiken selecteren voor tekst extractie
Voor grote documenten met meerdere pagina's gebruikt u de `pages` query parameter om pagina nummers of paginabereiken op te geven om alleen tekst uit die pagina's te halen. In het volgende voor beeld ziet u een document met 10 pagina's, waarbij de tekst wordt geëxtraheerd voor beide gevallen: alle pagina's (1-10) en geselecteerde pagina's (3-6).

:::image border type="content" source="../Images/ocr-select-pages.png" alt-text="Uitvoer van geselecteerde pagina's":::

## <a name="get-results-from-the-service"></a>Resultaten van de service ophalen

De tweede stap is het aanroepen van de bewerking [Lees resultaten ophalen](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d9869604be85dee480c8750) . Met deze bewerking wordt de bewerkings-ID die is gemaakt door de Lees bewerking, als invoer gebruikt. 

`https://{endpoint}/vision/v3.2/read/analyzeResults/{operationId}`

Er wordt een JSON-antwoord geretourneerd dat een **status** veld met de volgende mogelijke waarden bevat. 

|Waarde | Betekenis |
|:-----|:----|
| `notStarted`| De bewerking is niet gestart. |
| `running`| De bewerking wordt verwerkt. |
| `failed`| De bewerking is mislukt. |
| `succeeded`| De bewerking is voltooid. |

U roept deze bewerking iteratief aan tot deze met de **geslaagde** waarde wordt geretourneerd. Gebruik een interval van 1 tot 2 seconden om te voor komen dat het aantal aanvragen per seconde (RPS) wordt overschreden.

> [!NOTE]
> De laag gratis beperkt het aanvraag aantal tot 20 aanroepen per minuut. De laag betaald staat 10 aanvragen per seconde (RPS) toe die op aanvraag kunnen worden verhoogd. Noteer de identfier en de regio van uw Azure-resource, open een ondersteunings ticket voor Azure of neem contact op met uw account team om een RPS-frequentie (hogere aanvraag per seconde) aan te vragen.

Wanneer het veld **status** de `succeeded` waarde heeft, bevat het JSON-antwoord de geëxtraheerde tekst inhoud van uw afbeelding of document. De JSON-respons houdt de oorspronkelijke regel groeperingen van herkende woorden bij. Het bevat de geëxtraheerde tekst regels en de coördinaten van het begrenzingsvak. Elke tekst regel bevat alle geëxtraheerde woorden met hun coördinaten en betrouwbaarheids scores.

> [!NOTE]
> De gegevens die naar de `Read` bewerking worden verzonden, worden tijdelijk versleuteld en op rest opgeslagen voor een korte periode en vervolgens verwijderd. Hiermee kunnen uw toepassingen de geëxtraheerde tekst ophalen als onderdeel van de service reactie.

### <a name="sample-json-output"></a>Voor beeld van JSON-uitvoer

Zie het volgende voor beeld van een geslaagde JSON-reactie:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>Handgeschreven classificatie voor tekst regels (alleen Latijnse talen)
Het antwoord bevat het classificeren of elke tekst regel van het opmaak profiel is of niet, samen met een betrouwbaarheids Score. Deze functie wordt alleen ondersteund voor Latijnse talen. In het volgende voor beeld ziet u de handgeschreven classificatie voor de tekst in de afbeelding.

:::image border type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Voor beeld van de classificatie van OCR-hand schrift":::

## <a name="next-steps"></a>Volgende stappen

Als u de REST API wilt uitproberen, gaat u naar de [Lees API-naslag informatie](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
