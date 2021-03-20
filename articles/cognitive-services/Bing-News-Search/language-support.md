---
title: Taal ondersteuning-Bing Nieuws zoeken-API
titleSuffix: Azure Cognitive Services
description: Een lijst met natuurlijke talen, landen en regio's die worden ondersteund door de Bing Nieuws zoeken-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c7fd03c2239cea05dc79ad4dd1965fe253a2ce9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341594"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Ondersteuning van talen en regio's voor de Bing Nieuws zoeken-API

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Het Bing Nieuws zoeken-API ondersteunt talrijke landen/regio's, veel met meer dan één taal. Het opgeven van een land/regio met een query is voornamelijk bedoeld voor het verfijnen van de zoek resultaten op basis van interesses in dat land/deze regio. Daarnaast kunnen de resultaten koppelingen naar Bing bevatten en kunnen deze koppelingen de Bing-gebruikers ervaring lokaliseren volgens het opgegeven land/regio of taal.

U kunt een land/regio opgeven met behulp van de `cc` query parameter. Als u een land/regio opgeeft, moet u ook een of meer taal codes opgeven met de `Accept-Language` http-header. De ondersteunde talen variëren per teleenheid/regio. ze worden vermeld voor elk land/elke regio in de tabel markten.

U kunt ook de markt opgeven met behulp `mkt` van de query parameter en een code uit de tabel **markten** . Als u een markt opgeeft, geeft u een land/regio en een voorkeurs taal op. `setLang`In dit geval kan de query parameter worden ingesteld op een taal code. Dit is meestal dezelfde taal die wordt opgegeven door `mkt` tenzij de gebruiker de voor keur geeft om Bing te zien in een andere taal.

## <a name="supported-markets-for-news-search-endpoint"></a>Ondersteunde markten voor het zoeken naar nieuws-eind punten

Voor het `/news/search` eind punt bevat de volgende tabel de waarden van de markt code die u kunt gebruiken om de query parameter op te geven `mkt` . Bing retourneert inhoud alleen voor deze markten. De lijst is onderhevig aan wijzigingen.  

Voor een lijst met land-of regio codes die u in de `cc` query parameter kunt opgeven, raadpleegt u [land codes](#countrycodes).  

|Land/regio|Taal|Markt code|  
|---------------------|--------------|-----------------|
|Denemarken|Deens|da-DK|
|Oostenrijk|Duits|de-AT|
|Zwitserland|Duits|de-CH|
|Duitsland|Duits|de-DE|
|Australië|Engels|en-AU|
|Canada|Engels|en-CA|
|Verenigd Koninkrijk|Engels|en-GB|
|Indonesië|Engels|en-ID|
|Ierland|Engels|en-IE|
|India|Engels|en-IN|
|Maleisië|Engels|en-mijn|
|Nieuw-Zeeland|Engels|en-NZ|
|Republiek der Filippijnen|Engels|en-PH|
|Singapore|Engels|en-AG|
|Verenigde Staten|Engels|en-US|
|Engels|algemeen|en-WW|
|Engels|algemeen|en-XA|
|Zuid-Afrika|Engels|en-ZA|
|Argentinië|Spaans|es-AR|
|Chili|Spaans|es-LC|
|Spanje|Spaans|es-ES|
|Mexico|Spaans|es-MX|
|Verenigde Staten|Spaans|es-Verenigde Staten|
|Spaans|algemeen|es-XL|
|Finland|Fins|fi-FI|  
|Frankrijk|Frans|fr-worden|
|Canada|Frans|fr-CA|
|België|Nederlands|nl-worden|
|Zwitserland|Frans|fr-CH|
|Frankrijk|Frans|fr-FR|  
|Italië|Italiaans|it-IT|
|Hongkong SAR|Traditioneel Chinees|zh-HK|  
|Taiwan|Traditioneel Chinees|zh-TW|
|Japan|Japans|ja-JP|  
|Korea|Koreaans|ko-KR|  
|Nederland|Nederlands|nl-NL|  
|Volksrepubliek China|Chinees|zh-CN|  
|Brazilië|Portugees|pt-BR|
|Rusland|Russisch|ru-RU|  
|Zweden|Zweeds|sv-SE|  
|Turkije|Turks|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Ondersteunde markten voor nieuws eindpunt
Voor het `/news` eind punt bevat de volgende tabel de waarden van de markt code die u kunt gebruiken om de query parameter op te geven `mkt` . Bing retourneert inhoud alleen voor deze markten. De lijst is onderhevig aan wijzigingen.  

Voor een lijst met land-of regio codes die u in de `cc` query parameter kunt opgeven, raadpleegt u [land codes](#countrycodes).  

|Land/regio|Taal|Markt code|  
|---------------------|--------------|-----------------|
|Denemarken|Deens|da-DK|
|Duitsland|Duits|de-DE|
|Australië|Engels|en-AU|
|Verenigd Koninkrijk|Engels|en-GB|
|Verenigde Staten|Engels|en-US|
|Engels|algemeen|en-WW|
|Chili|Spaans|es-LC|
|Mexico|Spaans|es-MX|
|Verenigde Staten|Spaans|es-Verenigde Staten|
|Finland|Fins|fi-FI|  
|Canada|Frans|fr-CA|
|Frankrijk|Frans|fr-FR|  
|Italië|Italiaans|it-IT|
|Brazilië|Portugees|pt-BR|
|Volksrepubliek China|Chinees|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Ondersteunde markten voor het verloop van nieuws-eind punten
Voor het `/news/trendingtopics` eind punt bevat de volgende tabel de waarden van de markt code die u kunt gebruiken om de query parameter op te geven `mkt` . Bing retourneert inhoud alleen voor deze markten. De lijst is onderhevig aan wijzigingen.  

Voor een lijst met land-of regio codes die u in de `cc` query parameter kunt opgeven, raadpleegt u [land codes](#countrycodes).  

|Land/regio|Taal|Markt code|  
|---------------------|--------------|-----------------|
|Duitsland|Duits|de-DE|
|Australië|Engels|en-AU|
|Verenigd Koninkrijk|Engels|en-GB|
|Verenigde Staten|Engels|en-US|
|Canada|Engels|en-CA|
|India|Engels|en-IN|
|Frankrijk|Frans|fr-FR|
|Canada|Frans|fr-CA|
|Brazilië|Portugees|pt-BR|
|Volksrepubliek China|Chinees|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Land codes  

Hier volgen de land/regio codes die u in de `cc` query parameter kunt opgeven. De lijst is onderhevig aan wijzigingen.  

|Land/regio|Landcode|  
|---------------------|------------------|  
|Argentinië|AR|  
|Australië|AU|  
|Oostenrijk|AT|  
|België|BE|  
|Brazilië|BR|  
|Canada|CA (consistentie en beschikbaarheid)|  
|Chili|CL|  
|Denemarken|DK|  
|Finland|FI|  
|Frankrijk|FR|  
|Duitsland|DE|  
|Hongkong SAR|HK|  
|India|IN|  
|Indonesië|Id|  
|Italië|IT|  
|Japan|JP|  
|Korea|KR|  
|Maleisië|MY|  
|Mexico|MX|  
|Nederland|NL|  
|Nieuw-Zeeland|NZ|  
|Noorwegen|NO|  
|Volksrepubliek China|CN|  
|Polen|PL|  
|Portugal|PT|  
|Republiek der Filippijnen|PH|  
|Rusland|RU|  
|Saoedi-Arabië|SA|  
|Zuid-Afrika|ZA|  
|Spanje|ES|  
|Zweden|SE|  
|Zwitserland|CH|  
|Taiwan|TW|  
|Turkije|TR|  
|Verenigd Koninkrijk|GB|  
|Verenigde Staten|VS|

## <a name="next-steps"></a>Volgende stappen
Zie [Nieuws zoeken API V7-referentie](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)voor meer informatie over de Bing News Search-eind punten.