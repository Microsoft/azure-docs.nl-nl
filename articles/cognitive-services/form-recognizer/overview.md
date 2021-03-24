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
ms.openlocfilehash: 4465f88e3b0ccab8eace1936f426af8dd32af27b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872248"
---
# <a name="what-is-form-recognizer"></a>Wat is Form Recognizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer is een cognitieve service waarmee u software voor geautomatiseerde gegevensverwerking kunt bouwen met behulp van machine learning-technologie. U kunt de service gebruiken om tekst, sleutel-waardeparen, servicemarkeringen, tabellen en structuur te identificeren in en te extraheren uit uw documenten&mdash;. De service retourneert gestructureerde gegevens, inclusief de relaties in het oorspronkelijke bestand, begrenzingsvak, betrouwbaarheid, en meer. U krijgt snel nauwkeurige resultaten die zijn afgestemd op uw specifieke inhoud zonder dat u veel handmatige ingrepen moet uitvoeren of heel deskundig moet zijn in de gegevenswetenschap. Gebruik Form Recognizer om gegevensvermelding in uw toepassingen te automatiseren, en documenten te verrijken met mogelijkheden voor zoeken.

Formulier herkenning bestaat uit aangepaste modellen voor document verwerking, vooraf ontwikkelde modellen voor facturen, bevestigingen, Id's en visite kaartjes en het indelings model. U kunt Form Recognizer-modellen aanroepen met behulp van een REST API of clientbibliotheek-SDK’s om de complexiteit te reduceren en deze te integreren in uw werkstroom of toepassing.

Deze documentatie bevat de volgende artikel typen:  

* [**Quick**](quickstarts/client-library.md) starts zijn aan de slag-instructies die u helpen bij het maken van aanvragen voor de service.  
* [**Hand leidingen**](build-training-data-set.md) bevatten instructies voor het gebruik van de service op meer specifieke of aangepaste manieren.  
* [**Concepten**](concept-layout.md) geven uitgebreide uitleg over de service functionaliteit en-functies.  
* [**Zelf studies**](tutorial-bulk-processing.md) zijn meer gidsen die laten zien hoe u de service kunt gebruiken als onderdeel in bredere zakelijke oplossingen.  

## <a name="form-recognizer-features"></a>Functies voor formulier herkenning

Met de formulier herkenning kunt u eenvoudig formulier gegevens ophalen en analyseren met de volgende functies:

* **[Indelings-API](#layout-api)** : tekst, selectiemarkeringen en tabelstructuren extraheren uit documenten, samen met de bijbehorende begrenzingsvakcoördinaten.
* **[Aangepaste modellen](#custom-models)** : sleutel-waardeparen, selectiemarkeringen en tabelgegevens extraheren uit formulieren. Deze modellen worden getraind met uw eigen gegevens, zodat ze zijn afgestemd op uw formulieren.

* **[Preconstrueerde modellen](#prebuilt-models)** : Haal gegevens op uit unieke document typen met vooraf samengestelde modellen. Momenteel zijn de volgende vooraf gebouwde modellen beschikbaar

  * [Facturen](./concept-invoices.md)
  * [Ontvangstbewijzen](./concept-receipts.md)
  * [Visitekaartjes](./concept-business-cards.md)
  * [ID-kaarten](./concept-identification-cards.md)


## <a name="get-started"></a>Aan de slag

Gebruik het hulp programma voor voorbeeld formulier herkenning om de lay-out, vooraf ontwikkelde modellen en een aangepast model voor uw documenten uit te proberen. U hebt een Azure-abonnement nodig ([**Maak er een gratis**](https://azure.microsoft.com/free/cognitive-services)) en een resource-eind punt en-sleutel van een [**formulier herkenning**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) om de formulier Recognizer-service uit te proberen.

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

> [!div class="nextstepaction"]
> [Form Recognizer proberen](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Form Recognizer proberen](https://fott.azurewebsites.net/)

---
Volg de [rest API Snelstartgids](./quickstarts/client-library.md) van de client om aan de slag te gaan met het extra heren van gegevens uit uw documenten. U wordt aangeraden de gratis service te gebruiken wanneer u de technologie leert. Houd er rekening mee dat het aantal gratis pagina's beperkt is tot 500 per maand.

U kunt ook de REST-voor beelden (GitHub) gebruiken om aan de slag te gaan. 

* Tekst, selectie markeringen en tabel structuur extra heren uit documenten
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
| **Indeling analyseren** | Een document dat is door gegeven als een stroom analyseren om tekst, selectie markeringen, tabellen en structuur uit het document te extra heren |
| **Aangepast model trainen**| Train een nieuw model om uw formulieren te analyseren met behulp van vijf formulieren van hetzelfde type. Stel de parameter _useLabelFile_ in op `true` om met handmatig gelabelde gegevens te trainen. |
| **Formulier analyseren** |Analyseer met uw aangepaste model een formulier dat is doorgegeven als een stroom, om tekst, sleutel-waardeparen en tabellen te extraheren uit het formulier.  |
| **Factuur analyseren** | Analyseer een factuur om belang rijke informatie, tabellen en andere factuur tekst te extra heren.|
| **Ontvangstbewijs analyseren** | Analyseer een ontvangstbewijsdocument voor het extraheren van belangrijke informatie en andere ontvangstbewijstekst.|
| **ID analyseren** | Analyseer een ID-kaart document voor het extra heren van belang rijke informatie en andere ID-kaart tekst.|
| **Visitekaartjes analyseren** | Analyseer een visitekaartje om belangrijke informatie en tekst te extraheren.|

### <a name="v21-preview"></a>[Preview van v2.1](#tab/v2-1)

Lees het [naslagmateriaal bij de REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) voor meer informatie. Als u bekend bent met een eerdere versie van de API, raadpleegt u het artikel [Nieuwe functies](./whats-new.md) voor meer informatie over recente wijzigingen.

### <a name="v20"></a>[v2.0](#tab/v2-0)

Lees het [naslagmateriaal bij de REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) voor meer informatie. Als u bekend bent met een eerdere versie van de API, raadpleegt u het artikel [Nieuwe functies](./whats-new.md) voor meer informatie over recente wijzigingen.

---

## <a name="layout-api"></a>Indelings-API

Met Form Recognizer kunnen tekst, selectiemarkeringen en tabelstructuur (de rij- en kolomnummers die zijn gekoppeld aan de tekst) worden geëxtraheerd met behulp van optische tekenherkenning (OCR) in high-definition en een verbeterd Deep Learning-model van documenten. Raadpleeg de conceptuele handleiding voor [Indeling](./concept-layout.md) voor meer informatie.

:::image type="content" source="./media/tables-example.jpg" alt-text="voorbeeld van tabellen" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Aangepaste modellen

Aangepaste Form Recognizer-modellen worden getraind naar uw eigen gegevens; u hebt slechts vijf voorbeelden van invoerformulieren nodig om te beginnen. Met een getraind documentverwerkingsmodel kunnen gestructureerde gegevens worden uitgevoerd waarin ook de relaties uit het oorspronkelijke formulierdocument zijn opgenomen. Nadat u het model hebt getraind, kunt u het testen, opnieuw trainen en hiermee uiteindelijk gegevens naar behoefte extraheren uit meer formulieren.

U hebt de volgende opties wanneer u aangepaste modellen traint: training met en zonder gelabelde gegevens.

### <a name="train-without-labels"></a>Trainen zonder labels

De formulier herkenning maakt gebruik van leren zonder toezicht om inzicht te krijgen in de indeling en relaties tussen velden en items in uw formulieren. Wanneer u uw invoerformulieren verzendt, clustert het algoritme de formulieren op type, detecteert welke sleutels en tabellen aanwezig zijn en koppelt waarden aan sleutels en items aan tabellen. Voor trainingen zonder labels is hand matige gegevens labelen of intensieve code ring en onderhoud niet vereist. u wordt aangeraden deze methode eerst te proberen.

Zie [Een set trainingsgegevens bouwen](./build-training-data-set.md) voor tips over het verzamelen van uw trainingsdocumenten.

### <a name="train-with-labels"></a>Trainen met labels

Wanneer u met gelabelde gegevens traint, gebruikt het model gecontroleerde lessen om belang rijke waarden te extra heren, met behulp van de gelabelde formulieren die u opgeeft. Gegevens met een label worden in betere modellen uitgevoerd en kunnen modellen produceren die met complexe formulieren of formulieren met waarden zonder sleutels werken.

De [indelings-API](#layout-api) wordt gebruikt voor de lay-out van het formulier om de verwachte grootte en posities van gedrukte en handgeschreven tekst elementen en uitpak tabellen te ontdekken. Vervolgens worden door de gebruiker opgegeven labels gebruikt voor het leren van de sleutel/waarde-associaties en-tabellen in de documenten. We raden u aan vijf handmatig gelabelde formulieren van hetzelfde type (dezelfde structuur) te gebruiken om aan de slag te gaan wanneer u een nieuw model traint, en zo nodig meer gelabelde gegevens toe te voegen om de nauwkeurigheid van het model te verbeteren. Met de functie voor formulier herkenning kunt u een model trainen om sleutel waardeparen en tabellen te extra heren met behulp van de leer mogelijkheden onder toezicht. 

[Aan de slag met de trainen met labels](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Vooraf gemaakte modellen

Form Recognizer bevat ook vooraf ontwikkelde modellen voor geautomatiseerde gegevensverwerking van unieke formuliertypen.

### <a name="prebuilt-invoice-model"></a>Vooraf gebouwd factureringsmodel

Het vooraf gemaakte factuur model haalt gegevens op uit facturen in verschillende indelingen en retourneert gestructureerde gegevens. Dit model extraheert belang rijke informatie zoals de factuur-ID, klant gegevens, Details van de leverancier, verzen ding, factuur, totaal, belasting, subtotaal, regel artikelen en meer. Daarnaast wordt het vooraf gemaakte factuur model getraind om alle tekst en tabellen op de factuur te analyseren en te retour neren. Raadpleeg de conceptuele handleiding voor [Facturen](./concept-invoices.md) voor meer informatie.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="voorbeeldfactuur" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Vooraf samengesteld model voor aankoopbewijzen

Het vooraf samengesteld model voor aankoopbewijzen wordt in Australië, Canada, het Verenigd Koninkrijk, India en de Verenigde Staten gebruikt voor het lezen van Engelse aankoopbewijzen&mdash;het type dat wordt gebruikt door restaurants, tankstations, winkels, enzovoort. Dit model haalt belangrijke informatie op, zoals de tijd en datum van de transactie, informatie over de verkoper, btw, regelitems, totalen, enzovoort. Daarnaast wordt het vooraf gedefinieerde ontvangstbewijs model getraind voor het analyseren en retour neren van alle tekst op een ontvangst bewijs. Raadpleeg de conceptuele handleiding voor [Verkoopbonnen](./concept-receipts.md) voor meer informatie.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="voorbeeld van aankoopbewijs" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Model met vooraf gemaakte identificatie (ID)-kaarten

Met het identificatie model (ID) kunt u belang rijke informatie ophalen uit de wereld wijde paspoorten en stuur programma-licenties van ons. Er worden gegevens geëxtraheerd, zoals de document-ID, de verval datum, de verval datum, de naam, het land, de regio, de door de machine Lees bare zone en nog veel meer. Raadpleeg de conceptuele hand leiding [identificatie (id)](./concept-identification-cards.md) voor meer informatie.

:::image type="content" source="./media/overview-id.jpg" alt-text="voor beeld-identificatie kaart" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Vooraf samengesteld model voor visitekaartjes

Het vooraf samengesteld model voor visitekaartjes haalt informatie, zoals naam, functie, adres, e-mail, bedrijf en telefoonnummers van de persoon op uit visitekaartjes in het Engels. Raadpleeg de conceptuele handleiding voor [Visitekaartjes](./concept-business-cards.md) voor meer informatie.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="voorbeeld van visitekaartje" lightbox="./media/overview-business-card.jpg":::

## <a name="input-requirements"></a>Vereisten voor invoer

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>On-premises implementeren met behulp van Docker-containers

[Gebruik Form Recognizer-containers (preview)](form-recognizer-container-howto.md) om API-functies on-premises te implementeren. Met deze docker-container kunt u de service dichter bij uw gegevens plaatsen voor naleving, beveiliging of andere operationele redenen.

## <a name="service-availability-and-redundancy"></a>Servicebeschikbaarheid en redundantie

### <a name="is-form-recognizer-service-zone-resilient"></a>Is de Form Recognizer-service zonetolerant?

Ja. De Form Recognizer-service is standaard zonetolerant.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Hoe kan ik de Form Recognizer-service zo configureren dat deze zonetolerant wordt?

Er is geen klantconfiguratie nodig om zonetolerantie in te schakelen. Zonetolerantie voor Form Recognizer-bronnen is standaard beschikbaar en wordt beheerd door de service zelf.

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle services van Cognitive Services, dienen ontwikkelaars die de Form Recognizer-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Probeer onze online tool en Quick start voor meer informatie over de formulier Recognizer-service.

* [**Hulp programma voor formulier herkenning**](https://fott-preview.microsoft.com/)
* [**Snelstartgids voor client bibliotheken en REST API**](quickstarts/client-library.md)
