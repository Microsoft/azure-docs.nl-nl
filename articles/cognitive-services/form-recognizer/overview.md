---
title: Wat is Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Met de Azure Form Recognizer-service kunt u sleutel-waardeparen en tabelgegevens in uw formulierdocumenten identificeren en extraheren, en kunt u belangrijke gegevens extraheren uit verkoopbonnen en visitekaartjes.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: geautomatiseerde gegevensverwerking, documentverwerking, geautomatiseerde gegevensinvoer, formulierverwerking
ms.openlocfilehash: 680bb612546aaffc167970c1c48a44159ef9af6f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518220"
---
# <a name="what-is-form-recognizer"></a>Wat is Form Recognizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer is een cognitieve service waarmee u software voor geautomatiseerde gegevensverwerking kunt bouwen met behulp van machine learning-technologie. U kunt de service gebruiken om tekst, sleutel-waardeparen, servicemarkeringen, tabellen en structuur te identificeren in en te extraheren uit uw documenten&mdash;. De service retourneert gestructureerde gegevens, inclusief de relaties in het oorspronkelijke bestand, begrenzingsvak, betrouwbaarheid, en meer. U krijgt snel nauwkeurige resultaten die zijn afgestemd op uw specifieke inhoud zonder dat u veel handmatige ingrepen moet uitvoeren of heel deskundig moet zijn in de gegevenswetenschap. Gebruik Form Recognizer om gegevensvermelding in uw toepassingen te automatiseren, en documenten te verrijken met mogelijkheden voor zoeken.

Form Recognizer bestaat uit aangepaste modellen voor documentverwerking, vooraf samengestelde modellen voor facturen, bonnen, ID's en visitekaartjes en het indelingsmodel. U kunt Form Recognizer-modellen aanroepen met behulp van een REST API of clientbibliotheek-SDK’s om de complexiteit te reduceren en deze te integreren in uw werkstroom of toepassing.

Deze documentatie bevat de volgende artikeltypen:  

* [**Quickstarts zijn**](quickstarts/client-library.md) aan de slag-instructies om u te begeleiden bij het indienen van aanvragen bij de service.  
* [**Instructiegidsen**](build-training-data-set.md) bevatten instructies voor het gebruik van de service op specifiekere of aangepaste manieren.  
* [**Concepten**](concept-layout.md) bieden uitgebreide uitleg over de servicefunctionaliteit en -functies.  
* [**Zelfstudies**](tutorial-bulk-processing.md) zijn langere handleidingen die laten zien hoe u de service kunt gebruiken als onderdeel van bredere bedrijfsoplossingen.  

## <a name="form-recognizer-features"></a>Form Recognizer functies

Met Form Recognizer kunt u eenvoudig formuliergegevens extraheren en analyseren met de volgende functies:

* **[Indelings-API](#layout-api)** : tekst, selectiemarkeringen en tabelstructuren extraheren uit documenten, samen met de bijbehorende begrenzingsvakcoördinaten.
* **[Aangepaste modellen](#custom-models)** : sleutel-waardeparen, selectiemarkeringen en tabelgegevens extraheren uit formulieren. Deze modellen worden getraind met uw eigen gegevens, zodat ze zijn afgestemd op uw formulieren.

* **[Vooraf gebouwde modellen:](#prebuilt-models)** gegevens extraheren uit unieke documenttypen met behulp van vooraf gebouwde modellen. Momenteel zijn de volgende vooraf gebouwde modellen beschikbaar

  * [Facturen](./concept-invoices.md)
  * [Ontvangstbewijzen](./concept-receipts.md)
  * [Visitekaartjes](./concept-business-cards.md)
  * [Identificatiekaarten (id)](./concept-identification-cards.md)


## <a name="get-started"></a>Aan de slag

Gebruik het hulpprogramma Form Recognizer om indeling uit te proberen, vooraf gebouwde modellen uit te proberen en een aangepast model voor uw documenten te trainen. U hebt een Azure-abonnement [**nodig**](https://azure.microsoft.com/free/cognitive-services)(gratis maken) en een Form Recognizer [**resource-eindpunt**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) en -sleutel om de Form Recognizer proberen.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

> [!div class="nextstepaction"]
> [Form Recognizer proberen](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Form Recognizer proberen](https://fott.azurewebsites.net/)

---
Volg de [quickstart Clientbibliotheek/REST API om](./quickstarts/client-library.md) aan de slag te gaan met het extraheren van gegevens uit uw documenten. U wordt aangeraden de gratis service te gebruiken wanneer u de technologie leert. Houd er rekening mee dat het aantal gratis pagina's beperkt is tot 500 per maand.

U kunt ook de REST-voorbeelden (GitHub) gebruiken om aan de slag te gaan - 

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
| **Indeling analyseren** | Een document analyseren dat is doorgegeven als een stroom om tekst, selectiemarkeringen, tabellen en structuur uit het document te extraheren |
| **Aangepast model trainen**| Train een nieuw model om uw formulieren te analyseren met behulp van vijf formulieren van hetzelfde type. Stel de parameter _useLabelFile_ in op `true` om met handmatig gelabelde gegevens te trainen. |
| **Formulier analyseren** |Analyseer met uw aangepaste model een formulier dat is doorgegeven als een stroom, om tekst, sleutel-waardeparen en tabellen te extraheren uit het formulier.  |
| **Factuur analyseren** | Analyseer een factuur om sleutelgegevens, tabellen en andere factuurteksten op te halen.|
| **Ontvangstbewijs analyseren** | Analyseer een ontvangstbewijsdocument voor het extraheren van belangrijke informatie en andere ontvangstbewijstekst.|
| **Analyse-id** | Analyseer een document met een id-kaart om sleutelgegevens en andere tekst van de id-kaart op te halen.|
| **Visitekaartjes analyseren** | Analyseer een visitekaartje om belangrijke informatie en tekst te extraheren.|

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

Lees het [naslagmateriaal bij de REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) voor meer informatie. Als u bekend bent met een eerdere versie van de API, raadpleegt u het artikel [Nieuwe functies](./whats-new.md) voor meer informatie over recente wijzigingen.

### <a name="v20"></a>[v2.0](#tab/v2-0)

Lees het [naslagmateriaal bij de REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync) voor meer informatie. Als u bekend bent met een eerdere versie van de API, raadpleegt u het artikel [Nieuwe functies](./whats-new.md) voor meer informatie over recente wijzigingen.

---

## <a name="layout-api"></a>Indelings-API

Met Form Recognizer kunnen tekst, selectiemarkeringen en tabelstructuur (de rij- en kolomnummers die zijn gekoppeld aan de tekst) worden geëxtraheerd met behulp van optische tekenherkenning (OCR) in high-definition en een verbeterd Deep Learning-model van documenten. Raadpleeg de conceptuele handleiding voor [Indeling](./concept-layout.md) voor meer informatie.

:::image type="content" source="./media/tables-example.jpg" alt-text="voorbeeld van tabellen" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Aangepaste modellen

Aangepaste Form Recognizer-modellen worden getraind naar uw eigen gegevens; u hebt slechts vijf voorbeelden van invoerformulieren nodig om te beginnen. Met een getraind documentverwerkingsmodel kunnen gestructureerde gegevens worden uitgevoerd waarin ook de relaties uit het oorspronkelijke formulierdocument zijn opgenomen. Nadat u het model hebt getraind, kunt u het testen, opnieuw trainen en hiermee uiteindelijk gegevens naar behoefte extraheren uit meer formulieren.

U hebt de volgende opties wanneer u aangepaste modellen traint: training met en zonder gelabelde gegevens.

### <a name="train-without-labels"></a>Trainen zonder labels

Form Recognizer wordt leren zonder super supervised gebruikt om inzicht te krijgen in de indeling en relaties tussen velden en vermeldingen in uw formulieren. Wanneer u uw invoerformulieren verzendt, clustert het algoritme de formulieren op type, detecteert welke sleutels en tabellen aanwezig zijn en koppelt waarden aan sleutels en items aan tabellen. Voor training zonder labels is geen handmatige gegevenslabeling of intensieve codering en onderhoud vereist. We raden u aan deze methode eerst te proberen.

Zie [Een set trainingsgegevens bouwen](./build-training-data-set.md) voor tips over het verzamelen van uw trainingsdocumenten.

### <a name="train-with-labels"></a>Trainen met labels

Wanneer u traint met gelabelde gegevens, gebruikt het model leren onder supervisie om interessewaarden te extraheren met behulp van de gelabelde formulieren die u op verstrekt. Gelabelde gegevens leiden tot beter presterende modellen en kunnen modellen produceren die werken met complexe formulieren of formulieren die waarden zonder sleutels bevatten.

Form Recognizer layout-API [gebruikt](#layout-api) om de verwachte grootten en posities van gedrukte en handgeschreven tekstelementen te leren en tabellen te extraheren. Vervolgens worden door de gebruiker opgegeven labels gebruikt om de sleutel/waarde-verbanden en tabellen in de documenten te leren. We raden u aan vijf handmatig gelabelde formulieren van hetzelfde type (dezelfde structuur) te gebruiken om aan de slag te gaan wanneer u een nieuw model traint, en zo nodig meer gelabelde gegevens toe te voegen om de nauwkeurigheid van het model te verbeteren. Form Recognizer het trainen van een model voor het extraheren van sleutelwaardeparen en tabellen met behulp van leermogelijkheden onder supervisie. 

[Aan de slag met de trainen met labels](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Vooraf gemaakte modellen

Form Recognizer bevat ook vooraf ontwikkelde modellen voor geautomatiseerde gegevensverwerking van unieke formuliertypen.

### <a name="prebuilt-invoice-model"></a>Vooraf gebouwd factureringsmodel

Het vooraf gebouwde factuurmodel extraheert gegevens uit facturen in verschillende indelingen en retourneert gestructureerde gegevens. Met dit model worden belangrijke informatie geëxtraheerd, zoals de factuur-id, klantgegevens, details van de leverancier, verzenden naar, factureren naar, totaal, belasting, subtotaal, regelitems en meer. Daarnaast wordt het vooraf gebouwde factuurmodel getraind om alle tekst en tabellen op de factuur te analyseren en te retourneren. Raadpleeg de conceptuele handleiding voor [Facturen](./concept-invoices.md) voor meer informatie.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="voorbeeldfactuur" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Vooraf samengesteld model voor aankoopbewijzen

Het vooraf samengesteld model voor aankoopbewijzen wordt in Australië, Canada, het Verenigd Koninkrijk, India en de Verenigde Staten gebruikt voor het lezen van Engelse aankoopbewijzen&mdash;het type dat wordt gebruikt door restaurants, tankstations, winkels, enzovoort. Dit model haalt belangrijke informatie op, zoals de tijd en datum van de transactie, informatie over de verkoper, btw, regelitems, totalen, enzovoort. Daarnaast wordt het vooraf gebouwde bonmodel getraind om alle tekst op een ontvangstbewijs te analyseren en te retourneren. Raadpleeg de conceptuele handleiding voor [Verkoopbonnen](./concept-receipts.md) voor meer informatie.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="voorbeeld van aankoopbewijs" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Vooraf gemaakt id-kaartenmodel

Met het model voor identificatiekaarten (id's) kunt u belangrijke informatie extraheren uit wereldwijde passports en Amerikaanse stuurprogrammalicenties. Er worden gegevens geëxtraheert, zoals de document-id, de vervaldatum, de vervaldatum, de naam, het land, de regio, de voor machines leesbare zone en meer. Zie de [conceptuele handleiding Identificatiekaarten (id)-kaarten](./concept-identification-cards.md) voor meer informatie.

:::image type="content" source="./media/overview-id.jpg" alt-text="voorbeeld van identificatiekaart" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Vooraf samengesteld model voor visitekaartjes

Het vooraf samengesteld model voor visitekaartjes haalt informatie, zoals naam, functie, adres, e-mail, bedrijf en telefoonnummers van de persoon op uit visitekaartjes in het Engels. Raadpleeg de conceptuele handleiding voor [Visitekaartjes](./concept-business-cards.md) voor meer informatie.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="voorbeeld van visitekaartje" lightbox="./media/overview-business-card.jpg":::

## <a name="input-requirements"></a>Vereisten voor invoer

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>On-premises implementeren met behulp van Docker-containers

[Gebruik Form Recognizer-containers (preview)](form-recognizer-container-howto.md) om API-functies on-premises te implementeren. Met deze Docker-container kunt u de service dichter bij uw gegevens brengen om redenen van naleving, beveiliging of andere operationele redenen.

## <a name="service-availability-and-redundancy"></a>Servicebeschikbaarheid en redundantie

### <a name="is-form-recognizer-service-zone-resilient"></a>Is de Form Recognizer-service zonetolerant?

Ja. De Form Recognizer-service is standaard zonetolerant.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Hoe kan ik de Form Recognizer-service zo configureren dat deze zonetolerant wordt?

Er is geen klantconfiguratie nodig om zonetolerantie in te schakelen. Zonetolerantie voor Form Recognizer-bronnen is standaard beschikbaar en wordt beheerd door de service zelf.

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle services van Cognitive Services, dienen ontwikkelaars die de Form Recognizer-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Probeer ons onlinehulpprogramma en quickstart voor meer informatie over de Form Recognizer service.

* [**Form Recognizer hulpprogramma**](https://fott-preview.azurewebsites.net/)
* [**Quickstart voor clientbibliotheek en REST API**](quickstarts/client-library.md)
