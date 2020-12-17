---
title: Wat is er nieuw in de Text Analytics-API
titleSuffix: Text Analytics - Azure Cognitive Services
description: In dit artikel vindt u informatie over nieuwe releases en functies voor de Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 625fe5f53318ebc641d102e679ef7fd0607ef683
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652838"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Wat is er nieuw in de Text Analytics-API?

De Text Analytics-API wordt doorlopend bijgewerkt. In dit artikel vindt u informatie over nieuwe releases en functies, zodat u op de hoogte blijft van recente ontwikkelingen.

## <a name="december-2020"></a>December 2020

* De prijs informatie voor de Text Analytics-API is [bijgewerkt](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)

## <a name="november-2020"></a>November 2020

* Een [Nieuw eind punt](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze) met Text Analytics-API v 3.1-Preview. 3 voor de nieuwe asynchrone [analyse-API](how-tos/text-analytics-how-to-call-api.md?tabs=analyze), die batch verwerking ondersteunt voor ner, PII en extractie bewerkingen voor sleutel zinnen.
* Een [Nieuw eind punt](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) met Text Analytics-API v 3.1-Preview. 3 voor de nieuwe asynchrone [Text Analytics voor de status](how-tos/text-analytics-for-health.md) gehoste API met ondersteuning voor batch verwerking.
* De hierboven vermelde nieuwe functies zijn alleen beschikbaar in de volgende regio's: `West US 2` , `East US 2` , `Central US` `North Europe` en `West Europe` regio's.
* Portugees (Brazilië) `pt-BR` wordt nu ondersteund in [sentimentanalyse](how-tos/text-analytics-how-to-sentiment-analysis.md) v3. x, te beginnen met de model versie `2020-04-01` . Het wordt toegevoegd aan de bestaande `pt-PT` ondersteuning voor Portugees.
* Bijgewerkte client Bibliotheken, waaronder asynchrone analyse en Text Analytics voor status bewerkingen. U kunt voor beelden vinden op GitHub:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)


> [!div class="nextstepaction"]
> [Meer informatie over Text Analytics-API v 3.1-Preview. 3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>Oktober 2020

* Hindi-ondersteuning voor Sentimentanalyse v3. x, te beginnen met model versie `2020-04-01` . 
* Model versie `2020-09-01` voor het v3/languages-eind punt, waarmee verbeterde taal detectie en nauw keurigere verbeteringen worden toegevoegd.
* V3 Beschik baarheid in Centraal-India en UAE-noord.

## <a name="september-2020"></a>September 2020

### <a name="general-api-updates"></a>Algemene API-updates

* Release van een nieuwe URL voor de open bare preview van Text Analytics v 3.1 voor het ondersteunen van updates voor de volgende benoemde v3-eind punten voor entiteits herkenning: 
    * `/pii` eind punt bevat nu de nieuwe `redactedText` eigenschap in de JSON van het antwoord waar gedetecteerde PII-entiteiten in de invoer tekst worden vervangen door een `*` voor elk teken van deze entiteiten.
    * `/linking` eind punt bevat nu de `bingID` eigenschap in de JSON van de reactie voor gekoppelde entiteiten.
* De volgende Text Analytics preview-API-eind punten zijn op 4e september 2020 buiten gebruik gesteld:
    * v 2.1-Preview
    * v 3.0-preview
    * v 3.0-Preview-versie. 1
    
> [!div class="nextstepaction"]
> [Meer informatie over Text Analytics-API v 3.1-Preview. 2](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>Text Analytics voor Status container updates

De volgende updates zijn specifiek voor de release van september van de Text Analytics alleen voor de status container.
* Er is een nieuwe container installatie kopie met een tag `1.1.013530001-amd64-preview` met de nieuwe model versie `2020-09-03` vrijgegeven aan de containerpreview-opslag plaats. 
* Deze model versie biedt verbeteringen in het herkennen van entiteiten, afkortings detectie en latentie verbeteringen.

> [!div class="nextstepaction"]
> [Meer informatie over Text Analytics status](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>Augustus 2020

### <a name="general-api-updates"></a>Algemene API-updates

* Model versie `2020-07-01` voor de V3 `/keyphrases` `/pii` en `/languages` eind punten die het volgende toevoegen:
    * Aanvullende overheids [Categorieën](named-entity-types.md?tabs=personal) en land instellingen voor benoemde entiteiten herkenning.
    * Ondersteuning voor Noors en Turks in Sentimentanalyse v3.
* Er wordt nu een HTTP 400-fout geretourneerd voor v3 API-aanvragen die de gepubliceerde [gegevens limieten](concepts/data-limits.md)overschrijden. 
* Eind punten die een offset retour neren, bieden nu ondersteuning voor de optionele `stringIndexType` para meter, waarmee de geretourneerde `offset` en `length` waarden worden aangepast, zodat deze overeenkomen met een ondersteund [reeks index schema](concepts/text-offsets.md).

### <a name="text-analytics-for-health-container-updates"></a>Text Analytics voor Status container updates

De volgende updates zijn specifiek voor de augustus-versie van de Text Analytics alleen voor de status container.

* Nieuwe model versie voor Text Analytics status: `2020-07-24`
* Nieuwe URL voor het verzenden van Text Analytics voor status aanvragen: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (als u de demo-web-app die is opgenomen in deze nieuwe container installatie kopie) wilt gebruiken, moet u ervoor zorgen dat de browser cache wordt gewist.

De volgende eigenschappen in het JSON-antwoord zijn gewijzigd:

* De naam van `type` is gewijzigd in `category` 
* De naam van `score` is gewijzigd in `confidenceScore`
* Entiteiten in het `category` veld van de JSON-uitvoer zijn nu in Pascal-gevallen. De naam van de volgende entiteiten is gewijzigd:
    * `EXAMINATION_RELATION` is gewijzigd in `RelationalOperator` .
    * `EXAMINATION_UNIT` is gewijzigd in `MeasurementUnit` .
    * `EXAMINATION_VALUE` is gewijzigd in `MeasurementValue` .
    * `ROUTE_OR_MODE` is hernoemd `MedicationRoute` .
    * De naam van de relationele entiteit `ROUTE_OR_MODE_OF_MEDICATION` is gewijzigd in `RouteOfMedication` .

De volgende entiteiten zijn toegevoegd:

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Relatie-extractie
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [Meer informatie over Text Analytics voor de status container](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>Juli 2020 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Text Analytics voor de status container-Public gated preview

De Text Analytics voor de status container bevindt zich nu in Public gated preview, waarmee u informatie kunt ophalen uit ongestructureerde tekst in het Engels in klinische documenten, zoals: patiënten-formulieren, notities van dokters, onderzoek documenten en kwijtings overzichten. Er wordt momenteel geen kosten in rekening gebracht voor Text Analytics voor het gebruik van de status container.

De container biedt de volgende functies:

* Herkenning van benoemde entiteiten
* Relatie-extractie
* Entiteiten koppelen
* Negatie

## <a name="may-2020"></a>Mei 2020

### <a name="text-analytics-api-v3-general-availability"></a>Text Analytics-API v3 algemene Beschik baarheid

De tekst analyse-API v3 is nu algemeen beschikbaar met de volgende updates:

* Model versie `2020-04-01`
* Nieuwe [gegevens limieten](concepts/data-limits.md) voor elke functie
* Bijgewerkte [taal ondersteuning](language-support.md) voor [sentimentanalyse (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Afzonderlijk eind punt voor entiteits koppeling 
* Nieuwe "adres" entiteits categorie in [named entity Recognition (ner) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Nieuwe subcategorieën in NER V3:
   * Locatie-geografische
   * Locatie-structureel
   * Organisatie-aandelen beurs
   * Organisatie-medisch
   * Organisatie-sporten
   * Gebeurtenis-cultureel
   * Gebeurtenis-natuurlijk
   * Evenement-sporten

De volgende eigenschappen in het JSON-antwoord zijn toegevoegd:
   * `SentenceText` in Sentimentanalyse
   * `Warnings` voor elk document 

De namen van de volgende eigenschappen in het JSON-antwoord zijn gewijzigd, indien van toepassing:

* De naam van `score` is gewijzigd in `confidenceScore`
    * `confidenceScore` heeft twee decimale punten nauw keurigheid. 
* De naam van `type` is gewijzigd in `category`
* De naam van `subtype` is gewijzigd in `subcategory`

> [!div class="nextstepaction"]
> [Meer informatie over Text Analytics-API v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Text Analytics-API v 3.1 open bare preview
   * Nieuwe Sentimentanalyse functie: [opinie analyse](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Nieuw persoonlijk ( `PII` ) domein filter voor beveiligde status informatie ( `PHI` ).

> [!div class="nextstepaction"]
> [Meer informatie over Text Analytics-API v 3.1 Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Februari 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK-ondersteuning voor de open bare preview van Text Analytics-API v3

Als onderdeel van de [Unified Azure SDK-versie](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)is de Text Analytics-API v3 SDK nu beschikbaar als open bare Preview voor de volgende programmeer talen:
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [Meer informatie over Text Analytics-API v3 SDK](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Named entity Recognition v3 open bare preview

Er zijn nu extra entiteits typen beschikbaar in de open bare preview-service van de NER (named entity Recognition) omdat we de detectie van algemene en persoonlijke informatie entiteiten in tekst kunnen uitbreiden. Deze update introduceert [model versie](concepts/model-versioning.md) `2020-02-01` , waaronder:

* Erkenning van de volgende algemene entiteits typen (alleen Engels):
    * PersonType
    * Product
    * Gebeurtenis
    * Geopolitieke entiteit (GPE) als subtype onder locatie
    * Vaardigheid

* Herkenning van de volgende entiteits typen van persoonlijke gegevens (alleen Engels):
    * Person
    * Organisatie
    * Leeftijd als subtype onder hoeveelheid
    * Datum als een subtype onder DateTime
    * E-mail 
    * Telefoon nummer (alleen VS)
    * URL
    * IP-adres

### <a name="october-2019"></a>Oktober 2019

#### <a name="named-entity-recognition-ner"></a>NER (Herkenning van benoemde entiteiten)

* Een [Nieuw eind punt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) voor het herkennen van entiteits typen van persoonlijke gegevens (alleen Engels)

* Afzonderlijke eind punten voor [entiteits herkenning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) en [entiteits koppeling](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking).

* [Model versie](concepts/model-versioning.md) `2019-10-01` , waaronder:
    * Uitgebreide detectie en categorisatie van entiteiten gevonden in tekst. 
    * Herkenning van de volgende nieuwe entiteits typen:
        * Telefoonnummer
        * IP-adres

Koppeling van entiteit ondersteunt Engels en Spaans. NER taal ondersteuning varieert per entiteits type.

#### <a name="sentiment-analysis-v3-public-preview"></a>Open bare preview van Sentimentanalyse v3

* Een [Nieuw eind punt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) voor het analyseren van sentiment.
* [Model versie](concepts/model-versioning.md) `2019-10-01` , waaronder:

    * Aanzienlijke verbeteringen in de nauw keurigheid en Details van de tekst categorisatie en Score van de API.
    * Automatische labeling voor verschillende gevoel in tekst.
    * Sentiment analyse en uitvoer op het niveau van een document en zin. 

Het biedt ondersteuning voor Engels ( `en` ), Japans ( `ja` ), vereenvoudigd Chinees ( `zh-Hans` ), traditioneel Chinees ( `zh-Hant` ), Frans ( `fr` ), Italiaans ( `it` ), Spaans (), `es` Nederlands (), Portugees () en `nl` `pt` Duits ( `de` ), en is beschikbaar in de volgende regio's: `Australia East` ,, `Central Canada` , `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` ,,,,,,, en. 

> [!div class="nextstepaction"]
> [Meer informatie over Sentimentanalyse v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Volgende stappen

* [Wat is Text Analytics-API?](overview.md)  
* [Voorbeeldgebruikerscenario's](text-analytics-user-scenarios.md)
* [Sentimentanalyse](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Taaldetectie](how-tos/text-analytics-how-to-language-detection.md)
* [Herkenning van entiteiten](how-tos/text-analytics-how-to-entity-linking.md)
* [Sleuteltermextractie](how-tos/text-analytics-how-to-keyword-extraction.md)
