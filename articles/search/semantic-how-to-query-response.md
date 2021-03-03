---
title: Een semantisch antwoord structureren
titleSuffix: Azure Cognitive Search
description: Hierin wordt het algoritme voor semantische classificatie in Cognitive Search beschreven en wordt uitgelegd hoe u ' semantische antwoorden ' en ' semantische bijschriften ' structureert vanuit een resultatenset.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: febbfd0f3def8e681107ef78d9478463b1c2a872
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679867"
---
# <a name="semantic-ranking-and-responses-in-azure-cognitive-search"></a>Semantische classificatie en reacties in azure Cognitive Search

> [!IMPORTANT]
> Het semantische classificatie algoritme en de semantische antwoorden/bijschriften bevinden zich in de open bare preview-versie, die alleen beschikbaar is via de preview-REST API. Preview-functies worden onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)aangeboden.

Met semantische classificatie wordt de nauw keurigheid van de zoek resultaten verbeterd door de beste overeenkomsten te rangschikken met behulp van een semantisch classificatie model dat is getraind voor query's in natuurlijke taal, in tegens telling tot tref woorden.

In dit artikel wordt het algoritme voor semantische classificatie beschreven en wordt uitgelegd hoe een semantisch antwoord wordt gevormd. Een antwoord omvat bijschriften, zowel in tekst zonder opmaak als met hooglichten en antwoorden (optioneel).

+ Semantische bijschriften zijn tekst die relevant zijn voor de query die is geëxtraheerd uit de zoek resultaten. Ze kunnen u helpen een samen vatting te geven van een resultaat wanneer afzonderlijke inhouds velden te groot zijn voor de resultaten pagina. Bijschriften voorzien in semantische hooglichten, zodat gebruikers snel skim kunnen zoeken naar de meest relevante documenten, waardoor de algehele gebruikers ervaring wordt verbeterd.

+ Semantische antwoorden gebruiken machine learning modellen van Bing om antwoorden te formuleren op query's die eruitzien als vragen. De antwoorden worden geselecteerd uit een lijst met door Voer die het meest relevant zijn voor de query, zoals geëxtraheerd uit de bovenste documenten in de resultatenset van de query. Antwoorden worden geretourneerd als een onafhankelijk object op het hoogste niveau in de nettolading van het query antwoord dat u kunt weer geven op de zoek pagina's, naast de resultaten van de zoek opdracht.

## <a name="prerequisites"></a>Vereisten

+ Query's die zijn geformuleerd met behulp van het type semantische query. Zie [een semantische query maken](semantic-how-to-query-request.md)voor meer informatie.

## <a name="understanding-a-semantic-response"></a>Uitleg over een semantisch antwoord

Een semantisch antwoord bevat nieuwe eigenschappen voor scores, bijschriften en antwoorden. Een semantisch antwoord wordt samengesteld op basis van het standaard antwoord, met de bovenste 50 resultaten die worden geretourneerd door de [Zoek machine met volledige tekst](search-lucene-query-architecture.md), die vervolgens opnieuw wordt geclassificeerd met behulp van de semantische rangorde. Als er meer dan 50 zijn opgegeven, worden de aanvullende resultaten geretourneerd, maar ze worden niet semantisch geclassificeerd.

Net als bij alle query's bestaat een antwoord uit alle velden die zijn gemarkeerd als ophaalbaar, of alleen die velden die worden vermeld in de SELECT-instructie. Het bevat ook het veld ' antwoord ' en ' bijschriften '.

+ Voor elk semantisch resultaat is er standaard één antwoord, geretourneerd als een afzonderlijk veld dat u kunt weer geven op een zoek pagina. U kunt Maxi maal vijf opgeven. De formulering van het antwoord wordt geautomatiseerd: alle documenten in de eerste resultaten doorzoeken, extractie samenvatting uitvoeren, gevolgd door Lees vaardigheid van de machine, en tenslotte een direct antwoord op de vraag van de gebruiker in het antwoord veld promo veren.

+ Een ' bijschrift ' is een uittreksel-gebaseerde samen vatting van document inhoud, geretourneerd als tekst zonder opmaak of met hooglichten. Bijschriften worden automatisch opgenomen en kunnen niet worden onderdrukt. Hooglichten worden toegepast met behulp van de Lees vaardigheid van de machine om te bepalen welke teken reeksen moeten worden benadrukt. Markeert uw aandacht aan de meest relevante gangen, zodat u snel een pagina met resultaten kunt scannen om het juiste document te vinden.

Een semantisch opnieuw gerangschikte resultaatset resulteert in orders met de @search.rerankerScore waarde. Als u een OrderBy-component toevoegt, wordt een HTTP 400-fout geretourneerd omdat die component niet wordt ondersteund in een semantische query.

## <a name="example"></a>Voorbeeld

De @search.rerankerScore bestaat naast de standaard @search.score en wordt gebruikt om de resultaten opnieuw te rangschikken.

Met de volgende query:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "answers": "none",
    "searchFields": "HotelName,Category,Description",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

U kunt verwachten dat u het volgende resultaat krijgt, representatief is voor een semantisch antwoord. Deze resultaten geven alleen de bovenste drie antwoorden weer, zoals gerangschikt op ' @search.rerankerScore '. U ziet hoe Oceanside redmiddel nu eerst wordt gerangschikt. Onder de standaard classificatie van ' @search.score "wordt dit resultaat geretourneerd op de tweede plaats, na het einde van de sporen.

```http
{
    "@odata.count": 31,
    "@search.answers": [],
    "value": [
        {
            "@search.score": 1.8920634,
            "@search.rerankerScore": 1.1091284966096282,
            "@search.captions": [
                {
                    "text": "Oceanside Resort. Budget. New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
                    "highlights": "<em>Oceanside Resort.</em> Budget. New Luxury Hotel.  Be the first to stay.<em> Bay views</em> from every room, location near the piper, rooftop pool, waterfront dining & more."
                }
            ],
            "HotelId": "18",
            "HotelName": "Oceanside Resort",
            "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the piper, rooftop pool, waterfront dining & more.",
            "Category": "Budget"
        },
        {
            "@search.score": 2.5204072,
            "@search.rerankerScore": 1.0731962407007813,
            "@search.captions": [
                {
                    "text": "Trails End Motel. Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
                    "highlights": "<em>Trails End Motel.</em> Luxury. Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
                }
            ],
            "HotelId": "40",
            "HotelName": "Trails End Motel",
            "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
            "Category": "Luxury"
        },
        {
            "@search.score": 1.4104348,
            "@search.rerankerScore": 1.06992666143924,
            "@search.captions": [
                {
                    "text": "Winter Panorama Resort. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
                    "highlights": "<em>Winter Panorama Resort</em>. Resort and Spa. Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs."
                }
            ],
            "HotelId": "12",
            "HotelName": "Winter Panorama Resort",
            "Description": "Newly-renovated with large rooms, free 24-hr airport shuttle & a new restaurant. Rooms/suites offer mini-fridges & 49-inch HDTVs.",
            "Category": "Resort and Spa"
        }
```

## <a name="next-steps"></a>Volgende stappen

+ [Overzicht van semantisch zoeken](semantic-search-overview.md)
+ [Vergelijkbaarheidsalgoritme](index-ranking-similarity.md)
+ [Een semantische query maken](semantic-how-to-query-request.md)
+ [Een basisquery maken](search-query-create.md)