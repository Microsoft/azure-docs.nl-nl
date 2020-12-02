---
title: Wat is de Bing-API voor zoeken naar lokale bedrijven?
titleSuffix: Azure Cognitive Services
description: De Bing-API voor zoeken naar lokale bedrijven is een RESTful-service waarmee uw toepassingen informatie over lokale plaatsen en bedrijven kunnen vinden op basis van zoekquery's.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 7a5829623707797b98593f837d6cadf009410f31
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487011"
---
# <a name="what-is-bing-local-business-search"></a>Wat is Bing Local Business Search?

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.
De Bing Local Business Search-API is een RESTful-service waarmee uw toepassingen informatie over lokale bedrijven kunnen vinden op basis van zoekopdrachten. Bijvoorbeeld `q=<business-name> in Redmond, Washington` of `q=Italian restaurants near me`. 

## <a name="features"></a>Functies
| Functie | Beschrijving |  
| -- | -- | 
| [Lokale bedrijven en plaatsen zoeken](quickstarts/local-quickstart.md) | De Bing Local Business Search-API geeft gelokaliseerde resultaten weer op basis van een zoekopdracht. De resultaten omvatten een URL naar de bedrijfswebsite, tekst, een telefoonnummer en een geografische locatie, waaronder: GPS-coördinaten, plaats, adres |  
| [Lokale resultaten filteren op geografische grenzen](specify-geographic-search.md) | Voeg coördinaten toe als zoekparameters om de resultaten te beperken tot een specifiek geografisch gebied, op basis van een straal of een vierkant begrenzingsvak. | 
| [Lokale bedrijfsresultaten filteren op categorie](local-categories.md) | Zoek naar lokale bedrijfsresultaten per categorie. Deze optie maakt gebruik van omgekeerde IP-locatie of GPS-coördinaten van de aanroeper om gelokaliseerde resultaten in verschillende bedrijfscategorieën te retourneren.|

## <a name="workflow"></a>Werkstroom
Roep de Bing Local Business Search-API aan vanuit een programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON-antwoorden kunnen worden geparseerd. Deze service is toegankelijk via de REST API.
 
1. Maak een [account voor de Cognitive Services-API](../cognitive-services-apis-create-account.md) met toegang tot de Bing Search-API's. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/cognitive-services/) maken.   
2. Pas URL-codering toe op uw zoektermen voor de zoekopdrachtparameter `q=""`. Bijvoorbeeld `q=nearby+restaurant` of `q=nearby%20restaurant`. Stel indien nodig ook de paginering in. 
3. Stuur een [aanvraag naar de Bing Local Business Search-API](quickstarts/local-quickstart.md). 
4. Het JSON-antwoord parseren 

> [!NOTE]
> Local Business Search biedt momenteel: 
> * Alleen ondersteuning voor de markt `en-US` 
> * Geen ondersteuning voor Bing Automatische suggesties 

## <a name="next-steps"></a>Volgende stappen
- [Zoekopdrachten en antwoorden](local-search-query-response.md)
- [Quickstart over Business Search](quickstarts/local-quickstart.md)
- [Referentie voor de Bing-API voor zoeken naar lokale bedrijven](local-search-reference.md)
- [Vereisten voor gebruik en weergave](../bing-web-search/use-display-requirements.md)