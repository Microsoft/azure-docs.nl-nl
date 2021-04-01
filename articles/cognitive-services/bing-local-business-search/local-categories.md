---
title: Zoek naar categorieën voor de Bing lokale zakelijke zoek-API
titleSuffix: Azure Cognitive Services
description: In dit artikel leest u hoe u zoek categorieën kunt opgeven voor het eind punt van de Bing Local Business Search-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 19b769d1fff431f95c20e607c17747f2ff483d2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96487181"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Zoek naar categorieën voor de Bing lokale zakelijke zoek-API

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de Bing Local Business Search-API kunt u zoeken naar lokale bedrijfs entiteiten in verschillende categorieën, met prioriteit aan de hand van resultaten die de locatie van een gebruiker sluiten. U kunt deze Zoek opdrachten gebruiken in Zoek opdrachten samen met de `localCircularView` `localMapView` [para meters](specify-geographic-search.md)en.


## <a name="toplevel-categories"></a>TopLevel Categorieën 

Met de volgende typen wordt de belangrijkste Zoek categorie gedefinieerd.  Er kan meer dan één categorie worden opgegeven met een door komma's gescheiden lijst die is toegewezen aan de `localCategories` para meter.  
- EatDrink 
- SeeDo 
- Aanschaffen 
- HotelsAndMotels 
- BanksAndCreditUnions 
- Parkeer 
- Zieken huizen 

## <a name="sub-categories"></a>Subcategorieën
Subcategorieën worden op dezelfde manier door gegeven als `localCategories` . Subcategorieën zijn meer specifieke categorieën. Ze zijn ondergeschikt in de zin dat als u een categorie C en een van de subcategorieën in dezelfde door komma's gescheiden lijst opgeeft, dezelfde resultaten krijgt als wanneer u C alleen hebt opgegeven.

### <a name="eat-drink"></a>Eten drinken

> BreweriesAndBrewPubs, CocktailLounges, AfricanRestaurants, AmericanRestaurants, bagels, BarbecueRestaurants, Taverns, SportsBars, Bar, BarsGrillsAndPubs, BuffetRestaurants | BelgianRestaurants, BritishRestaurants, CafeRestaurants, CaribbeanRestaurants, ChineseRestaurants, CoffeeAndTea, Delicatessens, DeliveryService, Diners, DiscountStores, donuts, FastFood, FrenchRestaurants, FrozenYogurt, GermanRestaurants, super markets, GreekRestaurants, boodschappen, HawaiianRestaurants, HungarianRestaurants, IceCreamAndFrozenDesserts, IndianRestaurants, ItalianRestaurants, JapaneseRestaurants, SAP, KoreanRestaurants, LiquorStores, MexicanRestaurants, MiddleEasternRestaurants, pizza, PolishRestaurants, PortugueseRestaurants, pretzels, restaurants, RussianAndUkrainianRestaurants, broods, SeafoodRestaurants, SpanishRestaurants, SteakHouseRestaurants, SushiRestaurants, maakt, ThaiRestaurants, TurkishRestaurants, VegetarianAndVeganRestaurants, VietnameseRestaurants

### <a name="see-do"></a>Zie do

> AmusementParks, attractions, Carnivals, Casinos, LandmarksAndHistoricalSites, MiniatureGolfCourses, MovieTheaters, musea, parken, SightseeingTours, TouristInformation, Zoos

### <a name="shop"></a>Aanschaffen

> AntiqueStores, Bookstores, CDAndRecordStores, ChildrensClothingStores, CigarAndTobaccoShops, ComicBookStores, DepartmentStores, DiscountStores, FleaMarketsAndBazaars, FurnitureStores, HomeImprovementStores, JewelryAndWatchesStores, KitchenwareStores, LiquorStores, MallsAndShoppingCenters, MensClothingStores, MusicStores, OutletStores, PetShops, PetSupplyStores, SchoolAndOfficeSupplyStores, ShoeStores, SportingGoodsStores, ToyAndGameStores, VitaminAndSupplementStores, WomensClothingStores


## <a name="examples-of-local-categories-search"></a>Voor beelden van lokale categorieën zoeken

In de volgende voor beelden worden resultaten OPGEHAALD volgens de `localCategories` para meter:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

Met de volgende query wordt het aantal ziekenhuis resultaten beperkt tot de eerste drie keer dat wordt geretourneerd door de Bing lokale Business Search-API:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

Het volgende voor beeld van een JSON-antwoord omvat drie zieken huizen in het grotere gebied van Seattle:

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen
- [Geografische Zoek grenzen](specify-geographic-search.md)
- [Zoekopdrachten en antwoorden](local-search-query-response.md)
- [Quick Start in C #](quickstarts/local-quickstart.md)