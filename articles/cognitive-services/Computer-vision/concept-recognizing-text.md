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
ms.openlocfilehash: 37a989082b63dc101bb519fea1cc4ef16c76ae49
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621532"
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

### <a name="read-32-preview-allows-selecting-pages"></a>Met de preview-versie van 3,2 lezen kunt u pagina ('s) selecteren
Met de [Preview-API lezen 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005)voor grote documenten met meerdere pagina's kunt u specifieke pagina nummers of paginabereiken opgeven als invoer parameter om alleen tekst uit die pagina's te halen. Dit is een nieuwe invoer parameter naast de optionele taal parameter.

> [!NOTE]
> **Taalinvoer** 
>
> De [Lees aanroep](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) heeft een optionele aanvraag parameter voor een taal. Dit is de taal code BCP-47 van de tekst in het document. Lees ondersteuning voor automatische taal identificatie en meertalige documenten, zodat u alleen een taal code kunt opgeven als u wilt afdwingen dat het document wordt verwerkt als die specifieke taal.

## <a name="the-read-call"></a>De Lees aanroep

De [Lees aanroep](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) van de Lees-API neemt een afbeeldings-of PDF-document als invoer en extraheert tekst asynchroon. De aanroep retourneert een veld met een antwoord header met de naam `Operation-Location` . De `Operation-Location` waarde is een URL die de bewerkings-id bevat die in de volgende stap moet worden gebruikt.

|Reactie header| Resultaten-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.1/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Facturering** 
>
> De pagina met [Computer Vision prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) bevat de prijs categorie voor lezen. Elke geanalyseerde afbeelding of pagina is één trans actie. Als u de bewerking aanroept met een PDF-of TIFF-document met 100 pagina's, telt de Lees bewerking deze als 100-trans acties en wordt er voor 100 trans acties gefactureerd. Als u 50-aanroepen hebt gedaan voor de bewerking en elke oproep een document met 100 pagina's heeft verzonden, wordt u gefactureerd voor 50 X 100 = 5000 trans acties.

## <a name="the-get-read-results-call"></a>De aanroep Lees resultaten ophalen

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
> De gegevens die naar de `Read` bewerking worden verzonden, worden tijdelijk versleuteld en opgeslagen in rust en binnen 48 uur verwijderd. Hiermee kunnen uw toepassingen de geëxtraheerde tekst ophalen als onderdeel van de service reactie.

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
### <a name="read-32-preview-adds-text-line-style-latin-languages-only"></a>Lees 3,2 Preview tekst lijn stijl toevoegen (alleen Latijnse talen)
De [lezen 3,2 Preview-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) voert een **vormgevings** object uit waarin wordt aangegeven of elke tekst regel moet worden afgedrukt of geschreven, samen met een betrouwbaarheids Score. Deze functie wordt alleen ondersteund voor Latijnse talen.

Ga aan de slag met de [Computer Vision rest API of de Quick](./quickstarts-sdk/client-library.md) start van de client bibliotheek om de OCR-mogelijkheden in uw toepassingen te integreren.

## <a name="supported-languages-for-print-text"></a>Ondersteunde talen voor afdruk tekst
De [Lees-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) biedt ondersteuning voor het extra heren van gedrukte tekst in het Engels, Spaans, Duits, Frans, Italiaans, Portugees en Nederlandstalige talen.

Zie de [ondersteunde talen](./language-support.md#optical-character-recognition-ocr) voor de volledige lijst met talen die door OCR worden ondersteund.

### <a name="read-32-preview-adds-simplified-chinese-and-japanese"></a>In de preview-versie van 3,2 wordt vereenvoudigd Chinees en Japans toegevoegd
De [open bare preview-versie van de lees 3,2-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) voegt ondersteuning toe voor vereenvoudigd Chinees en Japans. Als uw scenario ondersteuning vereist voor meer talen, raadpleegt u de sectie [OCR API](#ocr-api) . 

## <a name="supported-languages-for-handwritten-text"></a>Ondersteunde talen voor handgeschreven tekst
De Lees bewerking biedt momenteel alleen ondersteuning voor het extra heren van handgeschreven tekst in het Engels.

## <a name="use-the-rest-api-and-sdk"></a>De REST API en SDK gebruiken
De [rest API 3. x](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) is de voorkeurs optie voor de meeste klanten vanwege het gemak van integratie en snelle productiviteit. Azure en de Computer Vision service-afhandelings schaal, prestaties, gegevens beveiliging en nalevings behoeften terwijl u zich richt op het voldoen aan de behoeften van uw klanten.

## <a name="deploy-on-premise-with-docker-containers"></a>On-premises implementeren met docker-containers
De [Lees docker-container (preview)](./computer-vision-how-to-install-containers.md) biedt u de mogelijkheid om de nieuwe OCR-mogelijkheden in uw eigen lokale omgeving te implementeren. Containers zijn ideaal voor specifieke vereisten voor beveiliging en gegevensbeheer.

## <a name="example-outputs"></a>Voorbeeld uitvoer

### <a name="text-from-images"></a>Tekst van afbeeldingen

De volgende Lees-API-uitvoer toont de geëxtraheerde tekst van een afbeelding met verschillende tekst hoeken, kleuren en letter typen.

![Een afbeelding van verschillende woorden bij verschillende kleuren en hoeken, met geëxtraheerde tekst in de lijst](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Tekst uit documenten

Lees-API kan ook PDF-documenten als invoer hebben.

![Een factuur document, met de lijst geëxtraheerde tekst](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Handgeschreven tekst

Met de Lees bewerking wordt handgeschreven tekst uit afbeeldingen geëxtraheerd (momenteel alleen in het Engels).

![Een afbeelding van een handgeschreven notitie met geëxtraheerde tekst in de lijst](./Images/handwritten-example.png)

### <a name="printed-text"></a>Afgedrukte tekst

Met de Lees bewerking kan gedrukte tekst in verschillende talen worden uitgepakt.

![Een afbeelding van een Spaans Textbook, waarbij geëxtraheerde tekst wordt weer gegeven](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Gemengde taal documenten

De Lees-API ondersteunt afbeeldingen en documenten die meerdere talen bevatten, ook wel bekend als gemengde taal documenten. De functie werkt door elke tekst regel in het document in de gedetecteerde taal te classificeren voordat de tekst inhoud wordt geëxtraheerd.

![Een afbeelding van zinsdelen in verschillende talen, met geëxtraheerde tekst in de lijst](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR-API

De [OCR-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) maakt gebruik van een ouder herkennings model, ondersteunt alleen installatie kopieën en voert synchroon uit, wat direct met de gedetecteerde tekst wordt geretourneerd. Zie de [OCR-ondersteunde talen](./language-support.md#optical-character-recognition-ocr) en lees vervolgens de API.

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Net als bij alle cognitieve services moeten ontwikkel aars die de Lees/OCR-Services gebruiken, op de hoogte zijn van het micro soft-beleid voor klant gegevens. Zie de pagina Cognitive Services in het [micro soft vertrouwens centrum](https://www.microsoft.com/trust-center/product-overview) voor meer informatie.

> [!NOTE]
> De computer Vison 2,0 RecognizeText bewerkingen worden afgeschaft in het voor deel van de nieuwe Lees-API die in dit artikel wordt besproken. Bestaande klanten moeten [overstappen op het gebruik van Lees bewerkingen](upgrade-api-versions.md).

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met de [Computer Vision rest API of de Snelstartgids voor de client bibliotheek](./quickstarts-sdk/client-library.md).
- Meer informatie over de [lees rest API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Meer informatie over de [open bare preview-versie van 3,2 rest API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-1/operations/5d986960601faab4bf452005) met toegevoegde ondersteuning voor vereenvoudigd Chinees en Japans.