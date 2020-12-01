---
title: Zoek query's voor afbeeldingen aanpassen en suggereren-Bing Afbeeldingen zoeken-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het aanpassen van de zoek query's die u naar de Bing Afbeeldingen zoeken-API stuurt.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 2566b2cf950df915f8ea843c34ea1fb6f8e7ea21
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342002"
---
# <a name="customize-and-suggest-image-search-queries"></a>Zoek query's voor afbeeldingen aanpassen en suggereren

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

In dit artikel leest u hoe u query's kunt aanpassen en zoek termen kunt suggereren om naar de Bing Afbeeldingen zoeken-API te verzenden.

## <a name="suggest-search-terms"></a>Zoek termen Voorst Ellen

Als uw app een zoekvak bevat waarin zoek termen worden ingevoerd, kunt u de [Automatische suggestie-API voor Bing](../../bing-autosuggest/get-suggested-search-terms.md) gebruiken om de ervaring te verbeteren. De API kan voorgestelde zoek termen in realtime weer geven. De API retourneert voorgestelde query reeksen op basis van gedeeltelijke zoek termen en Cognitive Services.

## <a name="pivot-the-query"></a>De query draaien

Als Bing de oorspronkelijke Zoek query kan segmenteren, bevat het geretourneerde [afbeeldingen](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) -object `pivotSuggestions` . Suggesties voor draai grafieken kunnen worden weer gegeven als optionele zoek termen voor de gebruiker. Als de oorspronkelijke query bijvoorbeeld *micro soft-Opper vlak* was, kan de query door Bing worden gesegmenteerd in *micro soft* en het *Opper vlak* en kunnen er voor elke draai grafiek suggesties worden gegeven. Deze suggesties kunnen worden weer gegeven als optionele query termen aan de gebruiker.

In het volgende voor beeld ziet u de draai suggesties voor het *micro soft-Opper vlak*:  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Het veld `pivotSuggestions` bevat de lijst met segmenten (draaipunten) waarin de oorspronkelijke query is onderverdeeld. Voor elk draaipunt bevat de respons een lijst met [Query](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj)-objecten met voorgestelde query's. Het `text` veld bevat de voorgestelde query. Het `displayText` veld bevat de term waarmee de draai tabel in de oorspronkelijke query wordt vervangen. Een voor beeld is de release datum van het Opper vlak.

Als de query teken reeks van de draai tabel is wat de gebruiker zoekt, `text` gebruikt `thumbnail` u de velden en om de draai tabel query teken reeksen weer te geven. Zorg ervoor dat de miniatuur en de tekst kunnen worden geklikt met behulp van de `webSearchUrl` URL of de `searchLink` URL. Gebruiken `webSearchUrl` om de gebruiker naar de zoek resultaten van Bing te verzenden. Als u uw eigen resultaten pagina opgeeft, gebruikt u `searchLink` .

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>De query uitvouwen

Als Bing de query kan uitbreiden om de oorspronkelijke zoekopdracht te beperken, bevat het object [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) het veld `queryExpansions`. Als de query bijvoorbeeld *micro soft-Opper vlak* was, kunnen de uitgevouwen query's er als volgt uitzien:
- Micro soft Surface **Pro 3**.
- Micro soft Surface **RT**.
- Micro soft Surface **Phone**.
- Micro soft Surface **hub**.

In het volgende voorbeeld ziet u de uitgebreide query's voor *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Het veld `queryExpansions` bevat een lijst met [Query](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj)-objecten. Het `text` veld bevat de uitgevouwen query. Het `displayText` veld bevat de uitbreidings periode. Als de uitgevouwen query teken reeks is wat de gebruiker zoekt, gebruikt `text` `thumbnail` u de velden en om de uitgevouwen query reeksen weer te geven. Zorg ervoor dat de miniatuur en de tekst kunnen worden geklikt met behulp van de `webSearchUrl` URL of de `searchLink` URL. Gebruiken `webSearchUrl` om de gebruiker naar de zoek resultaten van Bing te verzenden. Als u uw eigen resultaten pagina opgeeft, gebruikt u `searchLink` .

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Aanvraagbeperkingen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Volgende stappen

Als u de Bing Afbeeldingen zoeken-API nog niet eerder hebt uitgevoerd, kunt u een [Snelstartgids](../quickstarts/csharp.md)proberen. Als u op zoek bent naar iets complexere, probeert u de zelf studie om een [Web-app met één pagina](../tutorial-bing-image-search-single-page-app.md)te maken.