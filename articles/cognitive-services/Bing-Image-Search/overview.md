---
title: Wat is de Bing Afbeeldingen zoeken-API?
titleSuffix: Azure Cognitive Services
description: "Met de Bing Afbeeldingen zoeken-API kunt u de cognitieve zoekfunctionaliteit voor afbeeldingen van Bing in uw toepassing gebruiken. Door gebruikerszoekquery's te verzenden met de API kunt u relevante en hoogwaardige afbeeldingen ophalen en weergeven die vergelijkbaar zijn met Bing: afbeeldingen."
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c52098d86efe60ee524735e6158add5260a0757f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338228"
---
# <a name="what-is-the-bing-image-search-api"></a>Wat is de Bing Afbeeldingen zoeken-API?

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de Bing Afbeeldingen zoeken-API kunt u de zoekfunctionaliteit voor afbeeldingen van Bing in uw toepassing gebruiken. Door zoekquery's te verzenden naar de API kunt u hoogwaardige afbeeldingen ophalen die vergelijkbaar zijn met [bing.com/images](https://www.bing.com/images).

Hoewel de Bing afbeeldingen zoeken-API alleen zoekresultaten met afbeeldingen genereert, kunt u de andere beschikbare [Bing zoeken-API's](../bing-web-search/bing-api-comparison.md) combineren of gebruiken om veel soorten inhoud te vinden op het web.

## <a name="bing-image-search-features"></a>Functies van Bing Afbeeldingen zoeken

| Functie                                                                                                                                                                                 | Beschrijving                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Zoektermen in realtime voorstellen](./concepts/bing-image-search-sending-queries.md) | Verbeter uw app-ervaring met de [Bing Automatische suggestie-API](../bing-autosuggest/get-suggested-search-terms.md) om voorgestelde zoektermen weer te geven wanneer deze worden getypt. |
| [Afbeeldingsresultaten filteren en beperken](./concepts/bing-image-search-get-images.md)                       | Filter de afbeeldingen die via Bing worden geretourneerd door queryparameters te bewerken.                                                                                                       |
| [Miniaturen bijsnijden en weergeven en het formaat hiervan wijzigen](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Bewerk miniatuurweergaven voor de afbeeldingen die zijn geretourneerd door Bing Afbeeldingen zoeken en geef deze weer.                                                                                      |
| [Gebruikerszoekquery's draaien en uitvouwen](./concepts/bing-image-search-sending-queries.md)               | Breid uw zoekmogelijkheden uit door voorgestelde zoektermen door Bing op te nemen en weer te geven in query's.                                                                    |
| [Trending afbeeldingen ophalen](trending-images.md)                                                                     | Pas een zoekopdracht voor trending afbeeldingen van over de hele wereld aan.                                                                                                          |

## <a name="workflow"></a>Werkstroom

De Bing Afbeeldingen zoeken-API is een RESTful-webservice die eenvoudig kan worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON kan worden geparseerd. U kunt de service gebruiken met de [REST API](./quickstarts/csharp.md) of de [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

1. Maak een [Account voor Cognitive Services-API](../cognitive-services-apis-create-account.md) met toegang tot de Bing Zoeken-API's. Als u geen Azure-abonnement hebt, kunt u gratis [een account maken](https://azure.microsoft.com/free/cognitive-services/).
2. Verzend een aanvraag naar de API met een geldige [zoekquery](./concepts/bing-image-search-sending-queries.md).
3. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="next-steps"></a>Volgende stappen

Probeer eerst de [interactieve demo](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) van de Bing Afbeeldingen zoeken-API uit.
In deze demo ziet u hoe u snel een zoekquery kunt aanpassen en internet kunt doorzoeken op afbeeldingen.

Als u snel aan de slag wilt met uw eerste API-aanvraag, kunt u het volgende leren:

* [Zoekquery's naar Bing](./quickstarts/csharp.md) verzenden met de REST API of
* De afbeeldingen [aanvragen en filteren](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) die door Bing worden geretourneerd met behulp van de SDK.

## <a name="see-also"></a>Zie tevens

* [Prijsinformatie](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) voor Bing Zoek-API's. 

* De referentiesectie [Bing Afbeeldingen zoeken-API versie 7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) bevat informatie over de API-eindpunten, headers, API-reacties en queryparameters.

* In de [Bing-vereisten voor gebruik en weergave](../bing-web-search/use-display-requirements.md) staan het acceptabele gebruik van de inhoud en informatie die is verkregen via de Bing Zoeken-API's.

* In het artikel [Afbeeldingen ophalen van internet met de Bing Afbeeldingen zoeken-API](./concepts/bing-image-search-get-images.md) wordt beschreven hoe u afbeeldingen kunt zoeken en ophalen van internet.

* In het artikel [Zoekquery's verzenden en ermee werken](./concepts/bing-image-search-sending-queries.md) wordt beschreven hoe u zoekquery's kunt maken, aanpassen en draaien.

* Bezoek de [hubpagina voor de Bing Search-API](../bing-web-search/overview.md) om de andere beschikbare API's te verkennen.