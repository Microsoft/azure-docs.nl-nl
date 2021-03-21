---
title: Optische teken herkenning (OCR)-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot optische teken herkenning (OCR) van afbeeldingen en documenten met gedrukte en handgeschreven tekst met behulp van de Computer Vision-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: e0247560afa8229f4fa5c25ec7dfbbca4f7defb2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102486095"
---
# <a name="optical-character-recognition-ocr"></a>Optische tekenherkenning (OCR)

De Computer Vision-API van Azure bevat functies voor optische teken herkenning (OCR) waarmee gedrukte of handgeschreven tekst uit afbeeldingen kan worden opgehaald. U kunt tekst extra heren uit afbeeldingen, zoals foto's van een licentie plaat of containers met serie nummers, en van documenten: facturen, facturen, financiële rapporten, artikelen en meer.

## <a name="read-api"></a>API lezen 

De Computer Vision [Lees-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) is de nieuwste OCR-technologie van Azure ([informatie over wat er nieuw](./whats-new.md)is) waarmee gedrukte tekst (in verschillende talen), handgeschreven tekst (alleen Engels), cijfers en valuta symbolen van afbeeldingen en PDF-documenten met meerdere pagina's worden geëxtraheerd. Het is geoptimaliseerd voor het extra heren van tekst uit tekst-zware afbeeldingen en PDF-documenten met meerdere pagina's met gemengde talen. Het biedt ondersteuning voor het detecteren van gedrukte en handgeschreven tekst in dezelfde afbeelding of hetzelfde document.

![Hoe OCR afbeeldingen en documenten converteert naar gestructureerde uitvoer met geëxtraheerde tekst](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Vereisten voor invoer
De **Lees** aanroep neemt afbeeldingen en documenten op als invoer. Ze hebben de volgende vereisten:

* Ondersteunde bestands indelingen: JPEG, PNG, BMP, PDF en TIFF
* Voor PDF-en TIFF-bestanden worden Maxi maal 2000 pagina's (alleen de eerste twee pagina's voor de gratis laag) verwerkt.
* De bestands grootte moet kleiner zijn dan 50 MB (4 MB voor de gratis laag) en afmetingen ten minste 50 x 50 pixels en Maxi maal 10000 x 10000 pixels. 
* De PDF-dimensies mogen Maxi maal 17 x 17 inch zijn, overeenkomen met de papier formaten Legal of a3 en kleiner.

> [!NOTE]
> **Taalinvoer** 
>
> De [Lees aanroep](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) heeft een optionele aanvraag parameter voor een taal. Lees ondersteuning voor automatische taal identificatie en meertalige documenten, zodat u alleen een taal code kunt opgeven als u wilt afdwingen dat het document wordt verwerkt als die specifieke taal.

## <a name="ocr-demo-examples"></a>OCR-demo (voor beelden)

![OCR-demo's](./Images/ocr-demo.gif)

## <a name="step-1-the-read-operation"></a>Stap 1: de Lees bewerking

De [Lees aanroep](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) van de Lees-API neemt een afbeeldings-of PDF-document als invoer en extraheert tekst asynchroon. De aanroep retourneert een veld met een antwoord header met de naam `Operation-Location` . De `Operation-Location` waarde is een URL die de bewerkings-id bevat die in de volgende stap moet worden gebruikt.

|Reactie header| Resultaten-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Facturering** 
>
> De pagina met [Computer Vision prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) bevat de prijs categorie voor lezen. Elke geanalyseerde afbeelding of pagina is één trans actie. Als u de bewerking aanroept met een PDF-of TIFF-document met 100 pagina's, telt de Lees bewerking deze als 100-trans acties en wordt er voor 100 trans acties gefactureerd. Als u 50-aanroepen hebt gedaan voor de bewerking en elke oproep een document met 100 pagina's heeft verzonden, wordt u gefactureerd voor 50 X 100 = 5000 trans acties.

## <a name="step-2-the-get-read-results-operation"></a>Stap 2: de bewerking Lees resultaten ophalen

De tweede stap is het aanroepen van de bewerking [Lees resultaten ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) . Met deze bewerking wordt de bewerkings-ID die is gemaakt door de Lees bewerking, als invoer gebruikt. Er wordt een JSON-antwoord geretourneerd dat een **status** veld met de volgende mogelijke waarden bevat. U roept deze bewerking iteratief aan tot deze met de **geslaagde** waarde wordt geretourneerd. Gebruik een interval van 1 tot 2 seconden om te voor komen dat het aantal aanvragen per seconde (RPS) wordt overschreden.

|Veld| Type | Mogelijke waarden |
|:-----|:----:|:----|
|status | tekenreeks | notStarted: de bewerking is niet gestart. |
| |  | uitgevoerd: de bewerking wordt verwerkt. |
| |  | mislukt: de bewerking is mislukt. |
| |  | geslaagd: de bewerking is voltooid. |

> [!NOTE]
> De laag gratis beperkt het aanvraag aantal tot 20 aanroepen per minuut. De laag betaald staat 10 aanvragen per seconde (RPS) toe die op aanvraag kunnen worden verhoogd. Gebruik het ondersteunings kanaal van Azure of uw account team om een RPS-rate (hogere aanvraag per seconde) aan te vragen.

Wanneer het veld **status** de waarde **geslaagd** heeft, bevat het JSON-antwoord de geëxtraheerde tekst inhoud van uw afbeelding of document. De JSON-respons houdt de oorspronkelijke regel groeperingen van herkende woorden bij. Het bevat de geëxtraheerde tekst regels en de coördinaten van het begrenzingsvak. Elke tekst regel bevat alle geëxtraheerde woorden met hun coördinaten en betrouwbaarheids scores.

> [!NOTE]
> De gegevens die naar de `Read` bewerking worden verzonden, worden tijdelijk versleuteld en op rest opgeslagen voor een korte periode en vervolgens verwijderd. Hiermee kunnen uw toepassingen de geëxtraheerde tekst ophalen als onderdeel van de service reactie.

## <a name="sample-json-output"></a>Voor beeld van JSON-uitvoer

Zie het volgende voor beeld van een geslaagde JSON-reactie:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="natural-reading-order-output-latin-only"></a>Uitvoer van natuurlijke Lees volgorde (alleen Latijn)
Met de [Lees-API voor lezen 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)geeft u de volg orde op waarin de tekst regels worden uitgevoerd met de `readingOrder` query parameter. Gebruik `natural` dit voor een meer mensen vriendelijke Lees volgorde, zoals wordt weer gegeven in het volgende voor beeld. Deze functie wordt alleen ondersteund voor Latijnse talen.

:::image border type="content" source="./Images/ocr-reading-order-example.png" alt-text="Voor beeld van OCR-Lees volgorde":::

## <a name="handwritten-classification-for-text-lines-latin-only"></a>Handgeschreven classificatie voor tekst regels (alleen Latijn)
Het antwoord op de preview-versie van de [Lees-3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) bevat classificatie voor elke tekst regel of niet, samen met een betrouwbaarheids Score. Deze functie wordt alleen ondersteund voor Latijnse talen. In het volgende voor beeld ziet u de handgeschreven classificatie voor de tekst in de afbeelding.

:::image border type="content" source="./Images/ocr-handwriting-classification.png" alt-text="Voor beeld van de classificatie van OCR-hand schrift":::

## <a name="select-pages-or-page-ranges-for-text-extraction"></a>Pagina ('s) of paginabereiken selecteren voor tekst extractie
Met de [lezen 3,2 Preview-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005)voor grote documenten met meerdere pagina's, gebruikt `pages` u de query parameter om pagina nummers of paginabereiken op te geven om alleen tekst uit die pagina's op te halen. In het volgende voor beeld ziet u een document met 10 pagina's, waarbij de tekst wordt geëxtraheerd voor beide gevallen: alle pagina's (1-10) en geselecteerde pagina's (3-6).

:::image border type="content" source="./Images/ocr-select-pages.png" alt-text="Uitvoer van geselecteerde pagina's":::

## <a name="supported-languages"></a>Ondersteunde talen
De Lees-Api's ondersteunen in totaal 73 talen voor de tekst van de afdruk stijl. Raadpleeg de volledige lijst met door [OCR ondersteunde talen](./language-support.md#optical-character-recognition-ocr). De handgeschreven stijl OCR wordt alleen ondersteund voor Engels.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>De Cloud-API gebruiken of on-premises implementeren
De lezen 3. x-Cloud-Api's zijn de voorkeurs optie voor de meeste klanten vanwege het gemak van integratie en snelle productiviteit. Azure en de Computer Vision service-afhandelings schaal, prestaties, gegevens beveiliging en nalevings behoeften terwijl u zich richt op het voldoen aan de behoeften van uw klanten.

Voor een on-premises implementatie kunt u met de [Lees docker-container (preview)](./computer-vision-how-to-install-containers.md) de nieuwe OCR-mogelijkheden in uw eigen lokale omgeving implementeren. Containers zijn ideaal voor specifieke vereisten voor beveiliging en gegevensbeheer.

## <a name="ocr-api"></a>OCR-API

De [OCR-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) maakt gebruik van een ouder herkennings model, ondersteunt alleen installatie kopieën en voert synchroon uit, wat direct met de gedetecteerde tekst wordt geretourneerd. Zie de [OCR-ondersteunde talen](./language-support.md#optical-character-recognition-ocr) en lees vervolgens de API.

> [!NOTE]
> De computer Vison 2,0 RecognizeText bewerkingen worden afgeschaft in het voor deel van de nieuwe Lees-API die in dit artikel wordt besproken. Bestaande klanten moeten [overstappen op het gebruik van Lees bewerkingen](upgrade-api-versions.md).

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met de [Computer Vision rest API of de Snelstartgids voor de client bibliotheek](./quickstarts-sdk/client-library.md).
- Meer informatie over de [lees 3,1-rest API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Meer informatie over de [open bare preview-versie van 3,2-lees rest API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) met ondersteuning voor een totaal van 73 talen.
