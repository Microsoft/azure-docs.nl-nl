---
title: Indelingen-formulier herkenning
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot de indelings analyse met de Form Recognizer API-gebruik en limieten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: a63f910b3a939e33b8c71d8f22d15f6d610a12cc
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845553"
---
# <a name="form-recognizer-layout-service"></a>Indelings service voor formulier herkenning

Azure Form Recognizer kan tekst, tabellen, selectie markeringen en structuur gegevens uit documenten ophalen met behulp van de lay-outservice. Met de indelings-API kunnen klanten documenten maken in verschillende indelingen en gestructureerde gegevens en representatie van het document retour neren. Het combineert onze krachtige functies voor [optische teken herkenning (OCR)](../computer-vision/concept-recognizing-text.md) met document over diepe leer modellen voor het extra heren van tekst, tabellen, selectie markeringen en de structuur van documenten. 

## <a name="what-does-the-layout-service-do"></a>Wat doet de lay-outservice?

De indelings-API extraheert tekst, tabellen, selectie markeringen en structuur gegevens van documenten met een uitzonderlijke nauw keurigheid en retourneert deze in een geordend Structured JSON-antwoord. Documenten kunnen uit verschillende indelingen en kwaliteit bestaan, inclusief door de telefoon vastgelegde installatie kopieën, gescande documenten en digitale Pdf's. De indelings-API haalt de gestructureerde uitvoer op uit al deze documenten.

![Voor beeld van indeling](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Probeer het eens

Als u de indelings service voor formulier herkenning wilt uitproberen, gaat u naar het hulp programma online voor beeld-UI:

> [!div class="nextstepaction"]
> [Voorbeeld-UI](https://fott-preview.azurewebsites.net/)

U hebt een Azure-abonnement nodig ([Maak er een gratis](https://azure.microsoft.com/free/cognitive-services)) en een resource-eind punt en-sleutel van een [formulier herkenning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) om de indelings-API voor formulier herkenning uit te proberen. 

![Scherm afbeelding van voor beeld UI; de tekst, tabellen en selectie markeringen van een document worden geanalyseerd](./media/analyze-layout.png)

### <a name="input-requirements"></a>Vereisten voor invoer 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>De bewerking voor het analyseren van de indeling

De bewerking voor het analyseren van een [indeling](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) vereist een document (afbeelding, TIFF of PDF-bestand) als invoer en extraheert de tekst, tabellen, selectie markeringen en de structuur van het document. De aanroep retourneert een veld met een antwoord header met de naam `Operation-Location` . De `Operation-Location` waarde is een URL die de resultaat-id bevat die in de volgende stap moet worden gebruikt.

|Reactie header| Resultaten-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>De resultaat bewerking voor het analyseren van de indeling ophalen

De tweede stap bestaat uit het aanroepen van de bewerking [resultaat analyse van de indeling ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult) . Met deze bewerking wordt de resultaat-ID gebruikt die is gemaakt door de bewerking voor het analyseren van de indeling. Er wordt een JSON-antwoord geretourneerd dat een **status** veld met de volgende mogelijke waarden bevat. 

|Veld| Type | Mogelijke waarden |
|:-----|:----:|:----|
|status | tekenreeks | `notStarted`: De analyse bewerking is niet gestart.<br /><br />`running`: De analyse bewerking wordt uitgevoerd.<br /><br />`failed`: De analyse bewerking is mislukt.<br /><br />`succeeded`: De analyse bewerking is voltooid.|

U roept deze bewerking iteratief aan totdat deze met de `succeeded` waarde wordt geretourneerd. Gebruik een interval van 3 tot 5 seconden om te voor komen dat het aantal aanvragen per seconde (RPS) wordt overschreden.

Wanneer het veld **status** de `succeeded` waarde heeft, bevat het JSON-antwoord de resultaten van de lay-outextractie, de tekst, de tabellen en de selectie markeringen die worden geëxtraheerd. De geëxtraheerde gegevens bevatten de geëxtraheerde tekst regels en woorden, het begrenzingsvak, de handgeschreven indicatie van de tekst, tabellen en selectie markeringen met een indicatie van de optie geselecteerd/niet geselecteerd. 

### <a name="sample-json-output"></a>Voor beeld van JSON-uitvoer

De reactie op de bewerking analyse van analyseren van resultaten ophalen is de gestructureerde weer gave van het document met alle gegevens die zijn geëxtraheerd. Hier ziet u een voor beeld van een [document bestand](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) en de uitvoer van de [voorbeeld indeling](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)van de gestructureerde uitvoer.

De JSON-uitvoer bestaat uit twee delen: 
* `"readResults"` het knoop punt bevat alle herkende tekst-en selectie markeringen. De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. 
* `"pageResults"` het knoop punt bevat de tabellen en cellen die zijn geëxtraheerd met hun selectie vakjes, betrouw baarheid en een verwijzing naar de regels en woorden in ' readResults '.

## <a name="example-output"></a>Voorbeeld uitvoer

### <a name="text"></a>Tekst

De indeling extraheert tekst uit documenten (PDF, TIFF) en afbeeldingen (jpg, PNG, BMP) met verschillende tekst hoeken, kleuren, hoeken, foto's van documenten, faxen, afgedrukt, handgeschreven (alleen Engels) en gemengde modi. Tekst wordt geëxtraheerd met informatie over lijnen, woorden, omsluitende kaders, betrouwbaarheids scores en stijl (handgeschreven of andere). Alle tekst informatie is opgenomen in de `"readResults"` sectie van de JSON-uitvoer. 

### <a name="tables"></a>Tabellen

De indeling extraheert tabellen uit documenten (PDF, TIFF) en afbeeldingen (jpg, PNG, BMP). Documenten kunnen worden gescand, fotografeerd of als cijfer worden beschouwd. Tabellen kunnen complexe tabellen zijn met samengevoegde cellen of kolommen, met of zonder randen en met oneven hoeken. Geëxtraheerde tabellen bevatten het aantal kolommen en rijen, het bereik en de kolom reeks. Elke cel wordt geëxtraheerd met het selectie kader en verwijzing naar de tekst die in de sectie is geëxtraheerd `"readResults"` . Tabel informatie bevindt zich in de `"pageResults"` sectie van de JSON-uitvoer. 

![Voor beeld van tabellen](./media/tables-example.jpg)

### <a name="selection-marks"></a>Selectie markeringen

De indeling haalt ook selectie markeringen uit documenten op. De door geëxtraheerde selectie markeringen zijn onder andere het begrenzingsvak, betrouw baarheid en status (geselecteerd/uitgeschakeld). Informatie over selectie markeringen wordt geëxtraheerd in de `"readResults"` sectie van de JSON-uitvoer. 

## <a name="next-steps"></a>Volgende stappen

- Probeer uw eigen indelings extractie met behulp van de voor [beeld-UI voor formulier herkenning](https://fott-preview.azurewebsites.net/)
- Voltooi de Quick Start van een [formulier herkenning](quickstarts/client-library.md) om te beginnen met het uitpakken van indelingen in de taal van uw keuze.

## <a name="see-also"></a>Zie ook

* [Wat is Form Recognizer?](./overview.md)
* [REST API referentie documenten](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)