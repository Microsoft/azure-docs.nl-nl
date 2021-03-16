---
title: Indelingen-formulier herkenning
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de indelings analyse met de Form Recognizer API-gebruik en limieten.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 73bef21f430bde1c6c2c95d7c3f685cccbbd9179
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467014"
---
# <a name="form-recognizer-layout-service"></a>Indelings service voor formulier herkenning

Azure Form Recognizer kan tekst, tabellen, selectie markeringen en structuur gegevens uit documenten ophalen met behulp van de lay-outservice. Met de indelings-API kunnen klanten documenten maken in verschillende indelingen en gestructureerde gegevens representaties van de documenten retour neren. Het combineert onze krachtige functies voor [optische teken herkenning (OCR)](../computer-vision/concept-recognizing-text.md) met diepe leer modellen voor het extra heren van tekst, tabellen, selectie markeringen en document structuur. 

## <a name="what-does-the-layout-service-do"></a>Wat doet de lay-outservice?

De indelings-API extraheert tekst, tabellen, selectie markeringen en structuur gegevens van documenten met een uitzonderlijke nauw keurigheid en retourneert een geordend, gestructureerd, JSON-antwoord. Documenten kunnen uit verschillende indelingen en kwaliteit bestaan, waaronder door de telefoon vastgelegde installatie kopieën, gescande documenten en digitale Pdf's. De indelings-API haalt nauw keurig de gestructureerde uitvoer van al deze documenten op.

![Voor beeld van indeling](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Probeer het eens

Als u de indelings service voor formulier herkenning wilt uitproberen, gaat u naar het hulp programma online voor beeld-UI:

> [!div class="nextstepaction"]
> [Formulier OCR testen (FOTT)](https://fott-preview.azurewebsites.net)

U hebt een Azure-abonnement nodig ([Maak er een gratis](https://azure.microsoft.com/free/cognitive-services)) en een resource-eind punt en-sleutel van een [formulier herkenning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) om de indelings-API voor formulier herkenning uit te proberen. 

![Scherm afbeelding van voor beeld UI; de tekst, tabellen en selectie markeringen van een document worden geanalyseerd](./media/analyze-layout.png)

### <a name="input-requirements"></a>Vereisten voor invoer 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>De bewerking voor het analyseren van de indeling

Roep eerst de bewerking voor het analyseren van de [indeling](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync) aan. Bij het analyseren van de indeling wordt een document (afbeelding, TIFF of PDF-bestand) gebruikt als invoer en worden de tekst, tabellen, selectie markeringen en de structuur van het document geëxtraheerd. De aanroep retourneert een veld met een antwoord header met de naam `Operation-Location` . De `Operation-Location` waarde is een URL die de resultaat-id bevat die in de volgende stap moet worden gebruikt.

|Reactie header| Resultaten-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>Uitvoer van natuurlijke Lees volgorde (alleen Latijn)

U kunt de volg orde opgeven waarin de tekst regels worden uitgevoerd met de `readingOrder` query parameter. Gebruik `natural` dit voor een meer mensen vriendelijke Lees volgorde, zoals wordt weer gegeven in het volgende voor beeld. Deze functie wordt alleen ondersteund voor Latijnse talen.

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="Voor beeld van indeling van Lees volgorde" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Pagina nummers of bereiken selecteren voor tekst extractie

Voor grote documenten met meerdere pagina's gebruikt u de `pages` query parameter om specifieke pagina nummers of paginabereiken voor tekst extractie aan te geven. In het volgende voor beeld ziet u een document met 10 pagina's, waarbij de tekst wordt geëxtraheerd voor beide gevallen: alle pagina's (1-10) en geselecteerde pagina's (3-6).

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="Uitvoer van geselecteerde pagina's in layout":::

## <a name="the-get-analyze-layout-result-operation"></a>De resultaat bewerking voor het analyseren van de indeling ophalen

De tweede stap bestaat uit het aanroepen van de bewerking [resultaat analyse van de indeling ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult) . Met deze bewerking wordt de resultaat-ID gebruikt die is gemaakt door de bewerking voor het analyseren van de indeling. Er wordt een JSON-antwoord geretourneerd dat een **status** veld met de volgende mogelijke waarden bevat. 

|Veld| Type | Mogelijke waarden |
|:-----|:----:|:----|
|status | tekenreeks | `notStarted`: De analyse bewerking is niet gestart.<br /><br />`running`: De analyse bewerking wordt uitgevoerd.<br /><br />`failed`: De analyse bewerking is mislukt.<br /><br />`succeeded`: De analyse bewerking is voltooid.|

Roep deze bewerking iteratief aan tot de waarde wordt geretourneerd `succeeded` . Gebruik een interval van 3 tot 5 seconden om te voor komen dat het aantal aanvragen per seconde (RPS) wordt overschreden.

Wanneer het veld **status** de `succeeded` waarde heeft, bevat het JSON-antwoord de geëxtraheerde indeling, tekst, tabellen en selectie markeringen. De geëxtraheerde gegevens omvatten geëxtraheerde tekst lijnen en woorden, begrenzingsvak, tekst weergave met handgeschreven aanduiding, tabellen en selectie markeringen met geselecteerde/niet-ingeschakelde vermeldingen. 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Handgeschreven classificatie voor tekst regels (alleen Latijn)

Het antwoord bevat het classificeren of elke tekst regel van het opmaak profiel is of niet, samen met een betrouwbaarheids Score. Deze functie wordt alleen ondersteund voor Latijnse talen. In het volgende voor beeld ziet u de handgeschreven classificatie voor de tekst in de afbeelding.

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="voor beeld van classificatie van hand schrift":::

### <a name="sample-json-output"></a>Voor beeld van JSON-uitvoer

De reactie op de bewerking *analyse voor analyseren van resultaten ophalen* is een gestructureerde weer gave van het document met alle gegevens die worden geëxtraheerd. Hier ziet u een voor beeld van een [document bestand](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) en de uitvoer van de [voorbeeld indeling](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)van de gestructureerde uitvoer.

De JSON-uitvoer bestaat uit twee delen:

* `readResults` het knoop punt bevat alle herkende tekst-en selectie markeringen. De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. 
* `pageResults` het knoop punt bevat de tabellen en cellen die zijn geëxtraheerd met hun selectie vakjes, betrouw baarheid en een verwijzing naar de regels en woorden in ' readResults '.

## <a name="example-output"></a>Voorbeeld uitvoer

### <a name="text"></a>Tekst

De indelings-API extraheert tekst uit documenten (PDF, TIFF) en afbeeldingen (JPG, PNG, BMP) met meerdere tekst hoeken en kleuren. Het accepteert foto's van documenten, faxen, gedrukte en/of handgeschreven tekst (alleen Engels) en gemengde modi. De tekst wordt geëxtraheerd met informatie over lijnen, woorden, omsluitende kaders, betrouw bare scores en stijl (handgeschreven of andere). Alle tekst informatie is opgenomen in de `readResults` sectie van de JSON-uitvoer. 

### <a name="tables"></a>Tabellen

Indelings-API extraheert tabellen uit documenten (PDF, TIFF) en afbeeldingen (JPG, PNG, BMP). Documenten kunnen worden gescand, fotografeerd of als cijfer worden beschouwd. Tabellen kunnen complex zijn met samengevoegde cellen of kolommen, met of zonder randen, en met oneven hoeken. Geëxtraheerde tabel informatie omvat het aantal kolommen en rijen, het bereik en de kolom reeks. Elke cel wordt geëxtraheerd met het selectie kader en verwijzing naar de tekst die in de sectie is geëxtraheerd `readResults` . Tabel informatie bevindt zich in de `pageResults` sectie van de JSON-uitvoer. 

![Voor beeld van tabellen](./media/tables-example.jpg)

### <a name="selection-marks"></a>Selectie markeringen

Met de indelings-API worden ook selectie markeringen uit documenten geëxtraheerd. Geëxtraheerde selectie markeringen bevatten het selectie kader, betrouw baarheid en status (geselecteerd/uitgeschakeld). Informatie over selectie markeringen wordt geëxtraheerd in de `readResults` sectie van de JSON-uitvoer. 

## <a name="next-steps"></a>Volgende stappen

* Probeer uw eigen indelings extractie met behulp van de voor [beeld-UI voor formulier herkenning](https://fott-preview.azurewebsites.net/)
* Voltooi de Quick Start van een [formulier herkenning](quickstarts/client-library.md) om de lay-outs in de ontwikkel taal van uw keuze te halen.

## <a name="see-also"></a>Zie ook

* [Wat is Form Recognizer?](./overview.md)
* [REST API referentie documenten](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
