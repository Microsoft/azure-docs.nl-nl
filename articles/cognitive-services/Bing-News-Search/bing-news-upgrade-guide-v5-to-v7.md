---
title: Upgrade Bing Nieuws zoeken-API v5 naar v7
titleSuffix: Azure Cognitive Services
description: Hiermee worden de onderdelen van uw Bing News Search toepassing geïdentificeerd die u moet bijwerken om versie 7 te gebruiken.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: a114cb24d79189f9e370fae1962f60ca97241d90
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351364"
---
# <a name="news-search-api-upgrade-guide"></a>Upgrade handleiding voor Nieuws zoeken-API

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Deze upgrade handleiding bevat de wijzigingen tussen versie 5 en versie 7 van de Bing Nieuws zoeken-API. Gebruik deze hand leiding om u te helpen bij het identificeren van de onderdelen van uw toepassing die u moet bijwerken om versie 7 te gebruiken.

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

### <a name="endpoints"></a>Eindpunten

- Het versie nummer van het eind punt is gewijzigd van v5 naar v7. Bijvoorbeeld `https://api.cognitive.microsoft.com/bing/v7.0/news/search`.

### <a name="error-response-objects-and-error-codes"></a>Fout bericht objecten en fout codes

- Alle mislukte aanvragen moeten nu een `ErrorResponse` object bevatten in de hoofd tekst van het antwoord.

- De volgende velden zijn toegevoegd aan het `Error` object.  
  - `subCode`&mdash;Partitioneert de fout code indien mogelijk naar discrete buckets
  - `moreDetails`&mdash;Aanvullende informatie over de fout die in het `message` veld wordt beschreven

- De V5-fout codes zijn vervangen door de volgende mogelijke `code` en `subCode` waarden.

|Code|SubCode|Beschrijving
|-|-|-
|Server Error|UnexpectedError<br/>ResourceError<br/>Niet geïmplementeerd|Bing retourneert server error wanneer een van de voor waarden van de onderliggende code optreedt. Het antwoord bevat deze fouten als de HTTP-status code 500 is.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Geblokkeerd|Bing retourneert InvalidRequest wanneer een deel van de aanvraag ongeldig is. Een vereiste para meter ontbreekt bijvoorbeeld of een parameter waarde is niet geldig.<br/><br/>Als de fout ParameterMissing of ParameterInvalidValue is, is de HTTP-status code 400.<br/><br/>Als de fout HttpNotAllowed is, wordt de HTTP-status code 410.
|RateLimitExceeded||Bing retourneert RateLimitExceeded wanneer u het quotum voor query's per seconde (QPS) of query's per maand (QPM) overschrijdt.<br/><br/>Bing retourneert HTTP-status code 429 als u QPS en 403 hebt overschreden als u QPM hebt overschreden.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing retourneert InvalidAuthorization wanneer Bing de oproepende functie niet kan verifiëren. De `Ocp-Apim-Subscription-Key` koptekst ontbreekt bijvoorbeeld of de abonnements sleutel is niet geldig.<br/><br/>Redundantie treedt op als u meer dan één verificatie methode opgeeft.<br/><br/>Als de fout InvalidAuthorization is, is de HTTP-status code 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing retourneert InsufficientAuthorization wanneer de aanroeper geen machtigingen heeft voor toegang tot de resource. Dit kan gebeuren als de abonnements sleutel is uitgeschakeld of is verlopen. <br/><br/>Als de fout InsufficientAuthorization is, is de HTTP-status code 403.

- De volgende fout codes worden toegewezen aan de nieuwe codes. Als u een afhankelijkheid van V5-fout codes hebt genomen, werkt u de code dienovereenkomstig bij.

|Versie 5-code|Versie 7 code. subcode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Uitgeschakeld|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|Server error. UnexpectedError
DataSourceErrors|Server error. ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Niet geïmplementeerd|Server error. niet geïmplementeerd
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Geblokkeerd|InvalidRequest. blocked

### <a name="object-changes"></a>Object wijzigingen

- Het veld is toegevoegd `contractualRules` aan het [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) -object. Het `contractualRules` veld bevat een lijst met regels die u moet volgen (bijvoorbeeld artikel toewijzing). U moet de toewijzing Toep assen die is opgenomen in in `contractualRules` plaats van met `provider` . Het artikel bevat `contractualRules` alleen wanneer de [webzoekopdrachten API](../bing-web-search/overview.md) -antwoord een nieuws antwoord bevat.

## <a name="non-breaking-changes"></a>Niet-brekende wijzigingen

### <a name="query-parameters"></a>Queryparameters

- U hebt producten toegevoegd als mogelijke waarde waarvoor u de [categorie](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) query parameter kunt instellen op. Zie [Categorieën per markt](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

- De [sortby](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) -query parameter is toegevoegd. Hiermee worden trends op datum gesorteerd op basis van de meest recente eerst.

- [De after](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) query-para meter is toegevoegd, waarmee trends in onderwerpen worden geretourneerd die door Bing zijn gedetecteerd op of na de opgegeven Unix-epoche-tijds tempel.

### <a name="object-changes"></a>Object wijzigingen

- Het veld is toegevoegd `mentions` aan het [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) -object. Het `mentions` veld bevat een lijst met entiteiten (personen of plaatsen) die in het artikel zijn gevonden.

- Het veld is toegevoegd `video` aan het [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) -object. Het `video` veld bevat een video die aan het nieuws artikel is gerelateerd. De video is een \<iframe\> die u kunt insluiten of een animatie miniatuur.

- Het veld is toegevoegd `sort` aan het [Nieuws](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) object. `sort`In het veld wordt de sorteer volgorde van de artikelen weer gegeven. De artikelen worden bijvoorbeeld gesorteerd op relevantie (standaard) of datum.

- Het [SortValue](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) -object is toegevoegd, waarmee een sorteer volgorde wordt gedefinieerd. Het `isSelected` veld geeft aan of het antwoord de sorteer volgorde heeft gebruikt. Als dit het **geval** is, wordt de sorteer volgorde gebruikt voor het antwoord. Als `isSelected` de waarde **False** is, kunt u de URL in het `url` veld gebruiken om een andere sorteer volgorde aan te vragen.