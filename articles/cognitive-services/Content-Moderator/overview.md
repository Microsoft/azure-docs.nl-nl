---
title: Wat is Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Leer hoe u met Content Moderator ongepaste inhoud in door gebruikers gegenereerd materiaal kunt traceren, markeren, beoordelen en filteren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: content moderator, azure content moderator, online moderator, content filtering software, content moderation service, content moderation
ms.openlocfilehash: a53611fdad84f06661f3b8928296b6a45851cea4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867267"
---
# <a name="what-is-azure-content-moderator"></a>Wat is Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Content Moderator is een AI-service waarmee u inhoud kunt afhandelen die mogelijk aanstootgevend, riskant of anderszins ongewenst is. Het bevat de op AI gebaseerde service om content te modereren waarmee tekst, afbeeldingen en video's worden gescand en automatisch content gemarkeerd wordt, evenals de Controletool, een online moderatoromgeving voor een team van mensen die inhoud beoordelen.

U kunt software toevoegen voor het filteren van inhoud aan uw app om te voldoen aan de regelgeving of om de beoogde omgeving voor gebruikers te behouden.

Deze documentatie bevat de volgende artikel typen:  

* [**Quick**](client-libraries.md) starts zijn aan de slag-instructies die u helpen bij het maken van aanvragen voor de service.  
* [**Hand leidingen**](try-text-api.md) bevatten instructies voor het gebruik van de service op meer specifieke of aangepaste manieren.  
* [**Concepten**](text-moderation-api.md) geven uitgebreide uitleg over de service functionaliteit en-functies.  
* [**Zelf studies**](ecommerce-retail-catalog-moderation.md) zijn meer gidsen die laten zien hoe u de service kunt gebruiken als onderdeel in bredere zakelijke oplossingen.  

## <a name="where-its-used"></a>Waar het wordt gebruikt

Hieronder vindt u enkele scenario's waarin een softwareontwikkelaar of -team een service nodig hebben om toezicht te houden op content:

- Onlinemarktplaatsen die toezicht willen houden op productcatalogi en andere door gebruikers gegenereerde inhoud.
- Gamingbedrijven die toezicht willen houden op door gebruikers gegenereerde gameartefacten en chatruimten.
- Platforms voor sociale berichten die toezicht willen houden op afbeeldingen, tekst en video's die door gebruikers worden toegevoegd.
- Grote mediabedrijven die gecentraliseerd toezicht implementeren voor hun inhoud.
- Leveranciers van oplossingen voor lager en voortgezet onderwijs die willen filteren op inhoud die ongepast is voor leerlingen en docenten.

> [!IMPORTANT]
> U kunt Content Moderator niet gebruiken om illegale afbeeldingen van kindermisbruik te detecteren. Gekwalificeerde organisaties kunnen echter het scherm [PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") gebruiken voor dit type inhoud.

## <a name="what-it-includes"></a>Samenstelling van Content Moderator

De Content Moderator-service bestaat uit verschillende webservice-API's die beschikbaar zijn via zowel REST-aanroepen als een SDK voor .NET. Hiertoe behoort ook het Beoordelingsprogramma, waarmee menselijke revisoren de service kunnen verbeteren of de toezichtfunctie kunnen aanpassen.

## <a name="moderation-apis"></a>Beheer-API's

De Content Moderator-service omvat Beheer-API’s, waarmee inhoud wordt gecontroleerd op materiaal dat mogelijk ongeschikt of aanstootgevend is.

![Blokdiagram voor Beheer-API's van Content Moderator](images/content-moderator-mod-api.png)

In de volgende tabel worden de verschillende typen Beheer-API's beschreven.

| API-groep | Beschrijving |
| ------ | ----------- |
|[**Beheer van tekst**](text-moderation-api.md)| Hiermee wordt tekst gescand op aanstootgevende inhoud, expliciet of suggestief seksuele inhoud, grof taalgebruik en persoonlijk gegevens.|
|[**Aangepaste terminologielijsten**](try-terms-list-api.md)| Hiermee wordt tekst vergeleken met een aangepaste lijst met termen, naast de ingebouwde termen. U kunt aangepaste lijsten gebruiken om inhoud te blokkeren of toe te staan volgens uw eigen beleidsregels voor inhoud.|  
|[**Beheer van afbeeldingen**](image-moderation-api.md)| Hiermee worden afbeeldingen gescand op erotische of racistische inhoud, wordt tekst in afbeeldingen gedetecteerd met de OCR-functie (optische tekenherkenning) en worden gezichten gedetecteerd.|
|[**Aangepaste afbeeldingslijsten**](try-image-list-api.md)| Hiermee worden afbeeldingen vergeleken met een aangepaste lijst van afbeeldingen. U kunt aangepaste afbeeldingslijsten gebruiken om te filteren op exemplaren van veelvoorkomende terugkerende inhoud die u niet opnieuw wilt classificeren.|
|[**Beheer van video**](video-moderation-api.md)| Hiermee worden video's gescand op erotische of racistische inhoud en worden tijdmarkeringen voor deze inhoud geretourneerd.|

## <a name="review-apis"></a>Beoordelings-API's

Met de Beoordelings-API’s kunt u uw beheerpijplijn integreren met menselijke revisoren. Gebruik de bewerkingen [Taken](review-api.md#jobs), [Beoordelingen](review-api.md#reviews) en [Werkstroom](review-api.md#workflows) om werkstromen voor menselijke beoordeling te maken en te automatiseren met het [Beoordelingsprogramma](#review-tool) (hieronder).

> [!NOTE]
> De werkstroom-API is nog niet beschikbaar in de .NET SDK, maar kan worden gebruikt met het REST-eindpunt.

![Blokdiagram voor Beoordelings-API's van Content Moderator](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Beoordelingsprogramma

De Content Moderator-service omvat ook het webgebaseerde [Beoordelingsprogramma](Review-Tool-User-Guide/human-in-the-loop.md) dat als host fungeert voor de inhoudsbeoordelingen van menselijke moderators. De service wordt niet getraind door menselijke invoer, maar de combinatie van het werk van de service en de menselijke beoordelingsteams zorgt voor een juiste balans tussen de efficiëntie en de nauwkeurigheid. Het Beoordelingsprogramma biedt ook een gebruikersvriendelijke front-end voor verschillende Content Moderator-resources.

![Startpagina voor het Beoordelingsprogramma van Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle services van Cognitive Services, dienen ontwikkelaars die de Content Moderator-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met Content Moderator in het webportaal, gaat u naar [Content Moderator op het web uitproberen](quick-start.md). U kunt ook een [quickstart voor de clientbibliotheek of REST API](client-libraries.md) doorlopen om het basisscenario met code te implementeren.