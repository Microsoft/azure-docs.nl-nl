---
title: Wat is Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Met de Azure Form Recognizer-service kunt u sleutel-waardeparen en tabelgegevens in uw formulierdocumenten identificeren en extraheren, en kunt u belangrijke gegevens extraheren uit verkoopbonnen en visitekaartjes.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: geautomatiseerde gegevensverwerking, documentverwerking, geautomatiseerde gegevensinvoer, formulierverwerking
ms.openlocfilehash: ed940622f72271ef3e606c5068babcb6366c31b6
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845533"
---
# <a name="what-is-form-recognizer"></a>Wat is Form Recognizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer is een cognitieve service waarmee u software voor geautomatiseerde gegevensverwerking kunt bouwen met behulp van machine learning-technologie. U kunt de service gebruiken om tekst, sleutel-waardeparen, servicemarkeringen, tabellen en structuur te identificeren in en te extraheren uit uw documenten&mdash;. De service retourneert gestructureerde gegevens, inclusief de relaties in het oorspronkelijke bestand, begrenzingsvak, betrouwbaarheid, en meer. U krijgt snel nauwkeurige resultaten die zijn afgestemd op uw specifieke inhoud zonder dat u veel handmatige ingrepen moet uitvoeren of heel deskundig moet zijn in de gegevenswetenschap. Gebruik Form Recognizer om gegevensvermelding in uw toepassingen te automatiseren, en documenten te verrijken met mogelijkheden voor zoeken.

Form Recognizer bestaat uit aangepaste modellen voor documentverwerking, vooraf gebouwde modellen voor facturen, ontvangstbewijzen en visitekaartjes, en het indelingsmodel. U kunt Form Recognizer-modellen aanroepen met behulp van een REST API of clientbibliotheek-SDK’s om de complexiteit te reduceren en deze te integreren in uw werkstroom of toepassing.

Form Recognizer bestaat uit de volgende services:
* **[Indelings-API](#layout-api)** : tekst, selectiemarkeringen en tabelstructuren extraheren uit documenten, samen met de bijbehorende begrenzingsvakcoördinaten.
* **[Aangepaste modellen](#custom-models)** : sleutel-waardeparen, selectiemarkeringen en tabelgegevens extraheren uit formulieren. Deze modellen worden getraind met uw eigen gegevens, zodat ze zijn afgestemd op uw formulieren.
* **[Vooraf samengestelde modellen](#prebuilt-models)** : gegevens ophalen uit unieke formuliertypen met vooraf samengestelde modellen. Momenteel zijn de volgende vooraf gebouwde modellen beschikbaar
    * [Facturen](./concept-invoices.md)
    * [Ontvangstbewijzen](./concept-receipts.md)
    * [Visitekaartjes](./concept-business-cards.md)


## <a name="try-it-out"></a>Probeer het eens

Als u de Form Recognizer-service wilt uitproberen, gaat u online naar de gebruikersinterface van het voorbeeldhulpprogramma:


# <a name="v20"></a>[v2.0](#tab/v2-0)
> [!div class="nextstepaction"]
> [Vooraf gebouwde modellen uitproberen](https://fott.azurewebsites.net/)

# <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)
> [!div class="nextstepaction"]
> [Vooraf gebouwde modellen uitproberen](https://fott-preview.azurewebsites.net/)

---

U hebt een Azure-abonnement ([maak gratis een account](https://azure.microsoft.com/free/cognitive-services)) en een eindpunt en sleutel voor de [Form Recognizer-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) nodig om de Form Recognizer-service te kunnen uitproberen.

## <a name="layout-api"></a>Indelings-API

Met Form Recognizer kunnen tekst, selectiemarkeringen en tabelstructuur (de rij- en kolomnummers die zijn gekoppeld aan de tekst) worden geëxtraheerd met behulp van optische tekenherkenning (OCR) in high-definition en een verbeterd Deep Learning-model van documenten. Raadpleeg de conceptuele handleiding voor [Indeling](./concept-layout.md) voor meer informatie.

:::image type="content" source="./media/tables-example.jpg" alt-text="voorbeeld van tabellen" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Aangepaste modellen

Aangepaste Form Recognizer-modellen worden getraind naar uw eigen gegevens; u hebt slechts vijf voorbeelden van invoerformulieren nodig om te beginnen. Met een getraind documentverwerkingsmodel kunnen gestructureerde gegevens worden uitgevoerd waarin ook de relaties uit het oorspronkelijke formulierdocument zijn opgenomen. Nadat u het model hebt getraind, kunt u het testen, opnieuw trainen en hiermee uiteindelijk gegevens naar behoefte extraheren uit meer formulieren.

U hebt de volgende opties wanneer u aangepaste modellen traint: training met en zonder gelabelde gegevens.

### <a name="train-without-labels"></a>Trainen zonder labels

Standaard gebruikt Form Recognizer leren zonder supervisie om informatie in te leren over de indeling en relaties tussen velden en items in uw formulieren. Wanneer u uw invoerformulieren verzendt, clustert het algoritme de formulieren op type, detecteert welke sleutels en tabellen aanwezig zijn en koppelt waarden aan sleutels en items aan tabellen. Hiervoor hoeft u niet handmatig gegevens te labelen, of intensief te coderen en onderhouden. U wordt aangeraden deze methode eerst te proberen.

Zie [Een set trainingsgegevens bouwen](./build-training-data-set.md) voor tips over het verzamelen van uw trainingsdocumenten.

### <a name="train-with-labels"></a>Trainen met labels

Wanneer u met gelabelde gegevens traint, leert het model onder supervisie om nuttige waarden te extraheren met behulp van de gelabelde formulieren die u opgeeft. Dit resulteert in betere presterende modellen en kan modellen produceren die met complexe formulieren of formulieren met waarden zonder sleutels kunnen werken.

De Form Recognizer gebruikt de [indelings-API](#layout-api) voor meer informatie over de verwachte grootten en posities van gedrukte en handgeschreven tekstelementen. Vervolgens worden door de gebruiker opgegeven labels gebruikt voor het leren van de sleutel/waarde-koppelingen in de documenten. We raden u aan vijf handmatig gelabelde formulieren van hetzelfde type (dezelfde structuur) te gebruiken om aan de slag te gaan wanneer u een nieuw model traint, en zo nodig meer gelabelde gegevens toe te voegen om de nauwkeurigheid van het model te verbeteren.

[Aan de slag met de trainen met labels](./quickstarts/label-tool.md)


> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


## <a name="prebuilt-models"></a>Vooraf gemaakte modellen

Form Recognizer bevat ook vooraf ontwikkelde modellen voor geautomatiseerde gegevensverwerking van unieke formuliertypen.

### <a name="prebuilt-invoice-model"></a>Vooraf gebouwd factureringsmodel
Met het vooraf gebouwde factureringsmodel worden gegevens uit facturen geëxtraheerd in verschillende indelingen, en geretourneerd als gestructureerde gegevens. Met dit model wordt belangrijke informatie geëxtraheerd, zoals factuur-id's, klantgegevens, gegevens van leveranciers, verzendadressen, factuuradressen, totalen, belastingen, en meer. Daarnaast wordt het vooraf gebouwde factureringsmodel getraind om alle tekst en tabellen op de factuur te herkennen en te retourneren. Raadpleeg de conceptuele handleiding voor [Facturen](./concept-invoices.md) voor meer informatie.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="voorbeeldfactuur" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Vooraf samengesteld model voor aankoopbewijzen

Het vooraf samengesteld model voor aankoopbewijzen wordt in Australië, Canada, het Verenigd Koninkrijk, India en de Verenigde Staten gebruikt voor het lezen van Engelse aankoopbewijzen&mdash;het type dat wordt gebruikt door restaurants, tankstations, winkels, enzovoort. Dit model haalt belangrijke informatie op, zoals de tijd en datum van de transactie, informatie over de verkoper, btw, regelitems, totalen, enzovoort. Daarnaast wordt het vooraf samengestelde ontvangstbewijsmodel getraind om alle tekst op een ontvangstbewijs te herkennen en te retourneren. Raadpleeg de conceptuele handleiding voor [Verkoopbonnen](./concept-receipts.md) voor meer informatie.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="voorbeeld van aankoopbewijs" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-business-cards-model"></a>Vooraf samengesteld model voor visitekaartjes

Het vooraf samengesteld model voor visitekaartjes haalt informatie, zoals naam, functie, adres, e-mail, bedrijf en telefoonnummers van de persoon op uit visitekaartjes in het Engels. Raadpleeg de conceptuele handleiding voor [Visitekaartjes](./concept-business-cards.md) voor meer informatie.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="voorbeeld van visitekaartje" lightbox="./media/overview-business-card.jpg":::


## <a name="get-started"></a>Aan de slag

Gebruik het [voorbeeldhulpprogramma voor Form Recognizer](https://fott.azurewebsites.net/) of volg een quickstart om aan de slag te gaan met het extraheren van gegevens uit uw formulieren. U wordt aangeraden de gratis service te gebruiken wanneer u de technologie leert. Houd er rekening mee dat het aantal gratis pagina's beperkt is tot 500 per maand.

* [Quickstart voor clientbibliotheek / REST API](./quickstarts/client-library.md) (alle talen, meerdere scenario's)
* Quickstarts voor webinterface
  * [Trainen met labels: voorbeeldhulpprogramma voor labelen](quickstarts/label-tool.md)
* REST-voorbeelden (GitHub)
 * Tekst, selectiemarkeringen en tabelstructuur extraheren uit documenten
    * [Indelingsgegevens extraheren - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
  * Aangepaste modellen trainen en formuliergegevens extraheren
    * [Trainen zonder labels - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
    * [Trainen met labels - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
  * Gegevens extraheren uit facturen
    * [Factuurgegevens extraheren - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
  * Gegevens extraheren uit aankoopbewijzen
    * [Ontvangstgegevens extraheren - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
  * Gegevens extraheren uit visitekaartjes
    * [Gegevens extraheren uit visitekaartjes - Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>De REST API's bekijken

U gebruikt de volgende API's om modellen te trainen en gestructureerde gegevens uit formulieren te extraheren.

|Naam |Beschrijving |
|---|---|
| **Indeling analyseren** | Analyseer een document dat is doorgegeven als een stroom, om tekst, selectiemarkeringen, tabellen en structuur te extraheren uit het document |
| **Aangepast model trainen**| Train een nieuw model om uw formulieren te analyseren met behulp van vijf formulieren van hetzelfde type. Stel de parameter _useLabelFile_ in op `true` om met handmatig gelabelde gegevens te trainen. |
| **Formulier analyseren** |Analyseer met uw aangepaste model een formulier dat is doorgegeven als een stroom, om tekst, sleutel-waardeparen en tabellen te extraheren uit het formulier.  |
| **Factuur analyseren** | Analyseer een factuur om belangrijke informatie, tabellen en andere factuurtekst te extraheren.|
| **Ontvangstbewijs analyseren** | Analyseer een ontvangstbewijsdocument voor het extraheren van belangrijke informatie en andere ontvangstbewijstekst.|
| **Visitekaartjes analyseren** | Analyseer een visitekaartje om belangrijke informatie en tekst te extraheren.|


# <a name="v20"></a>[v2.0](#tab/v2-0)
Lees het [naslagmateriaal bij de REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) voor meer informatie. Als u bekend bent met een eerdere versie van de API, raadpleegt u het artikel [Nieuwe functies](./whats-new.md) voor meer informatie over recente wijzigingen.

# <a name="v21"></a>[v2.1](#tab/v2-1)
Lees het [naslagmateriaal bij de REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeWithCustomForm) voor meer informatie. Als u bekend bent met een eerdere versie van de API, raadpleegt u het artikel [Nieuwe functies](./whats-new.md) voor meer informatie over recente wijzigingen.

---

## <a name="input-requirements"></a>Vereisten voor invoer

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>On-premises implementeren met behulp van Docker-containers

[Gebruik Form Recognizer-containers (preview)](form-recognizer-container-howto.md) om API-functies on-premises te implementeren. Deze Docker-container stelt u in staat om de service dichter bij uw gegevens te brengen voor naleving, beveiliging en andere operationele redenen.

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle services van Cognitive Services, dienen ontwikkelaars die de Form Recognizer-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voltooi een [quickstart](quickstarts/client-library.md) om aan de slag te gaan met het schrijven van een formulierverwerkingsapp met een Form Recognizer in de taal van uw keuze.