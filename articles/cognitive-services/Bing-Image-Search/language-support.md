---
title: Taal ondersteuning-Bing Afbeeldingen zoeken-API
titleSuffix: Azure Cognitive Services
description: Ontdek welke landen/regio's en talen worden ondersteund door de Bing Afbeeldingen zoeken-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 14dacc4351af444c75517df4d4e02ecca3bbf40a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350072"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Ondersteuning van talen en regio's voor de Bing Afbeeldingen zoeken-API

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Het Bing Afbeeldingen zoeken-API ondersteunt meer dan drie dozijn landen/regio's, veel met meer dan één taal. Het opgeven van een land/regio met een query is voornamelijk bedoeld voor het verfijnen van de zoek resultaten op basis van interesses in dat land/deze regio. Daarnaast kunnen de resultaten koppelingen naar Bing bevatten en kunnen deze koppelingen de Bing-gebruikers ervaring lokaliseren op basis van de opgegeven land/regio's of taal.

Als u het land/de regio en taal wilt opgeven, stelt `mkt` u de query parameter (Market) in op een code uit de tabel **markten** hieronder. Op de markt wordt een land/regio en taal opgegeven. Als de gebruiker de weer gave van tekst in een andere taal wilt zien, stelt `setLang` u de query parameter in op de juiste taal code.

U kunt ook het land/de regio opgeven met behulp van de `cc` query-para meter. Als u een land/regio opgeeft, moet u ook een of meer taal codes opgeven met de `Accept-Language` http-header. De ondersteunde talen variëren per land/regio. ze worden vermeld voor elk land/elke regio in de tabel markten.

> [!NOTE]
> De API voor trending-installatie kopieën ondersteunt momenteel alleen de volgende markten:
> - en-US (Engels, Verenigde Staten)
> - en-CA (Engels, Canada)
> - en-AU (Engels, Australië)
> - zh-CN (Chinees, China)

## <a name="countriesregions"></a>Landen/regio's

|Land/regio|Code|
|-------|----|
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
|China|CN|
|Polen|PL|
|Portugal|PT|
|Filipijnen|PH|
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


## <a name="markets"></a>Landen

|Land/regio|Taal|Markt code|
|-------|--------|-----------|
|Argentinië|Spaans|es-AR|
|Australië|Engels|en-AU|
|Oostenrijk|Duits|de-AT|
|België|Nederlands|nl-worden|
|België|Frans|fr-worden|
|Brazilië|Portugees|pt-BR|
|Canada|Engels|en-CA|
|Canada|Frans|fr-CA|
|Chili|Spaans|es-LC|
|Denemarken|Deens|da-DK|
|Finland|Fins|fi-FI|
|Frankrijk|Frans|fr-FR|
|Duitsland|Duits|de-DE|
|Hongkong SAR|Traditioneel Chinees|zh-HK|
|India|Engels|en-IN|
|Indonesië|Engels|en-ID|
|Italië|Italiaans|it-IT|
|Japan|Japans|ja-JP|
|Korea|Koreaans|ko-KR|
|Maleisië|Engels|en-mijn|
|Mexico|Spaans|es-MX|
|Nederland|Nederlands|nl-NL|
|Nieuw-Zeeland|Engels|en-NZ|
|China|Chinees|zh-CN|
|Polen|Pools|pl-PL|
|Portugal|Portugees|pt-PT|
|Filipijnen|Engels|en-PH|
|Rusland|Russisch|ru-RU|
|Saoedi-Arabië|Arabisch|ar-SA|
|Zuid-Afrika|Engels|en-ZA|
|Spanje|Spaans|es-ES|
|Zweden|Zweeds|sv-SE|
|Zwitserland|Frans|fr-CH|
|Zwitserland|Duits|de-CH|
|Taiwan|Traditioneel Chinees|zh-TW|
|Turkije|Turks|tr-TR|
|Verenigd Koninkrijk|Engels|en-GB|
|Verenigde Staten|Engels|en-US|
|Verenigde Staten|Spaans|es-Verenigde Staten|

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de Bing News Search-eind punten [News afbeeldingen zoeken API V7-referentie](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).