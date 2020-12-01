---
title: Veelgestelde vragen (FAQ)-Bing Afbeeldingen zoeken-API
titleSuffix: Azure Cognitive Services
description: Hier vindt u antwoorden op veelgestelde vragen over concepten, code en scenario's met betrekking tot de Bing Afbeeldingen zoeken-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: e8502b1d01e0f3fbf5d42e924511cc978690bce4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342189"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Veelgestelde vragen over de Bing Afbeeldingen zoeken-API

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Hier vindt u antwoorden op veelgestelde vragen over concepten, code en scenario's met betrekking tot de Bing Afbeeldingen zoeken-API voor Microsoft Cognitive Services op Azure.

## <a name="response-headers-in-javascript"></a>Antwoord headers in Java script

De volgende headers kunnen optreden in reacties van de Bing Afbeeldingen zoeken-API.

| Kenmerk           | Beschrijving   |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |De unieke ID die Bing aan de gebruiker heeft toegewezen |
| `BingAPIs-Market`   |De markt die is gebruikt om aan de aanvraag te voldoen |
| `BingAPIs-TraceId`  |De logboek vermelding op de Bing API-server voor deze aanvraag (voor ondersteuning) |

Het is vooral belang rijk dat u de client-ID persistent maakt en de volgende aanvragen retourneert. Wanneer u dit doet, wordt in de zoek opdracht in de context van de zoek resultaten gebruikgemaakt van de focus, en wordt ook een consistente gebruikers ervaring geboden.

Wanneer u echter de Bing Afbeeldingen zoeken-API aanroept vanuit Java script, kunnen de ingebouwde beveiligings functies (CORS) van uw browser ertoe leiden dat u geen toegang hebt tot de waarden van deze headers.

Als u toegang wilt krijgen tot de headers, kunt u de Bing Afbeeldingen zoeken-API aanvraag indienen via een CORS-proxy. Het antwoord van een dergelijke proxy heeft een `Access-Control-Expose-Headers` koptekst die antwoord headers filtert en deze beschikbaar maakt voor Java script.

Het is eenvoudig om een CORS-proxy te installeren zodat onze [zelf studie-app](tutorial-bing-image-search-single-page-app.md) toegang kan krijgen tot de optionele client headers. Als u [Node.js](https://nodejs.org/en/download/) nog niet hebt, moet u dit eerst installeren. Voer vervolgens de volgende opdracht in een opdrachtprompt in.

```console
npm install -g cors-proxy-server
```

Wijzig vervolgens het Bing Afbeeldingen zoeken-API-eind punt in het HTML-bestand in: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Start ten slotte de CORS-proxy met de volgende opdracht:

```console
cors-proxy-server
```

Laat het opdrachtvenster geopend terwijl u de zelfstudie-app gebruikt. Als u het venster sluit, wordt de proxy gestopt. In de uitbreidbare sectie met HTTP-headers onder de zoekresultaten ziet u nu (onder andere) de `X-MSEdge-ClientID`-header en kunt u controleren of deze voor elke aanvraag gelijk is.

## <a name="response-headers-in-production"></a>Antwoord headers in productie

De in het vorige antwoord beschreven methode voor CORS-proxy is geschikt voor ontwikkeling, testen en leren.

In een productie omgeving moet u echter een script aan de server zijde hosten in hetzelfde domein als de webpagina die gebruikmaakt van de Bing Webzoekopdrachten-API. Dit script moet de API-aanroepen op aanvraag van de webpagina-java script en alle resultaten, inclusief headers, terugsturen naar de client. Omdat de twee resources (pagina en script) een oorsprong delen, komt CORS niet in Play en zijn de speciale headers toegankelijk voor de Java script op de webpagina.

Deze benadering beschermt ook uw API-sleutel tegen bloot stelling aan het publiek, omdat alleen het script aan de server zijde dit vereist. Het script kan een andere methode (zoals de HTTP-verwijzings reeks) gebruiken om ervoor te zorgen dat de aanvraag wordt geautoriseerd.

## <a name="next-steps"></a>Volgende stappen

Is uw vraag over een ontbrekend onderdeel of functionaliteit? Overweeg om het te vragen of te stemmen op onze [website voor gebruikers spraak](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Zie ook

 [Stack Overflow: Cognitive Services](https://stackoverflow.com/questions/tagged/bing-api)