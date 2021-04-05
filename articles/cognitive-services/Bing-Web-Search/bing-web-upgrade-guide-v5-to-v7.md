---
title: Upgrade van API v5 naar V7-Bing Webzoekopdrachten-API
titleSuffix: Azure Cognitive Services
description: Bepaal welke onderdelen van uw toepassing updates moeten gebruiken voor het gebruik van de Bing Web Search V7-Api's.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d930543671a5328d76a38aa7e1b421c111e89e39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349517"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Upgrade van Bing Webzoekopdrachten-API v5 naar v7

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Deze upgrade handleiding bevat de wijzigingen tussen versie 5 en versie 7 van de Bing Webzoekopdrachten-API. Gebruik deze hand leiding om u te helpen bij het identificeren van de onderdelen van uw toepassing die u moet bijwerken om versie 7 te gebruiken.

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

### <a name="endpoints"></a>Eindpunten

- Het versie nummer van het eind punt is gewijzigd van v5 naar v7. Bijvoorbeeld https: \/ \/ API.Cognitive.Microsoft.com/Bing/**v 7.0**/Search.

### <a name="error-response-objects-and-error-codes"></a>Fout bericht objecten en fout codes

- Alle mislukte aanvragen moeten nu een `ErrorResponse` object bevatten in de hoofd tekst van het antwoord.

- De volgende velden zijn toegevoegd aan het `Error` object.  
  - `subCode`&mdash;Partitioneert de fout code indien mogelijk naar discrete buckets
  - `moreDetails`&mdash;Aanvullende informatie over de fout die in het `message` veld wordt beschreven


- De V5-fout codes zijn vervangen door de volgende mogelijke `code` en `subCode` waarden.

|Code|SubCode|Description
|-|-|-
|Server Error|UnexpectedError<br/>ResourceError<br/>Niet geïmplementeerd|Bing retourneert server error wanneer een van de voor waarden van de subcode zich voordoet. De respons bevat deze fouten als de HTTP-status code 500 is.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Geblokkeerd|Bing retourneert InvalidRequest wanneer een deel van de aanvraag ongeldig is. Een vereiste para meter ontbreekt bijvoorbeeld of een parameter waarde is niet geldig.<br/><br/>Als de fout ParameterMissing of ParameterInvalidValue is, is de HTTP-status code 400.<br/><br/>Als de fout HttpNotAllowed is, wordt de HTTP-status code 410.
|RateLimitExceeded||Bing retourneert RateLimitExceeded wanneer u het quotum voor query's per seconde (QPS) of query's per maand (QPM) overschrijdt.<br/><br/>Bing retourneert HTTP-status code 429 als u QPS en 403 hebt overschreden als u QPM hebt overschreden.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing retourneert InvalidAuthorization wanneer Bing de oproepende functie niet kan verifiëren. De `Ocp-Apim-Subscription-Key` koptekst ontbreekt bijvoorbeeld of de abonnements sleutel is niet geldig.<br/><br/>Redundantie treedt op als u meer dan één verificatie methode opgeeft.<br/><br/>Als de fout InvalidAuthorization is, is de HTTP-status code 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing retourneert InsufficientAuthorization wanneer de aanroeper geen machtigingen heeft voor toegang tot de resource. Deze fout kan optreden als de abonnements sleutel is uitgeschakeld of is verlopen. <br/><br/>Als de fout InsufficientAuthorization is, is de HTTP-status code 403.

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


## <a name="non-breaking-changes"></a>Niet-brekende wijzigingen  

### <a name="headers"></a>Kopteksten

- De optionele header [pragma](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) -aanvraag is toegevoegd. Bing retourneert standaard cache-inhoud, indien beschikbaar. Om te voorkomen dat Bing inhoud uit de cache retourneert, stelt u de Pragma-header in op no-cache (bijvoorbeeld: Pragma: no-cache).

### <a name="query-parameters"></a>Queryparameters

- De query parameter [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) is toegevoegd. Gebruik deze para meter om het aantal antwoorden op te geven dat u wilt dat het antwoord bevat. De antwoorden worden gekozen op basis van de rang schikking. Als u deze para meter bijvoorbeeld instelt op drie (3), bevat het antwoord de eerste drie geclassificeerde antwoorden.  

- De [promote](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) query-para meter is toegevoegd. Gebruik deze para meter `answerCount` om expliciet een of meer antwoord typen toe te voegen, ongeacht hun positie. Als u bijvoorbeeld Video's en afbeeldingen in het antwoord wilt promoten, stelt u promo veren in op *Video's, afbeeldingen*. De lijst met antwoorden die u wilt promoten, telt niet op basis van de `answerCount` limiet. Als bijvoorbeeld `answerCount` 2 is en `promote` is ingesteld op *Video's, installatie kopieën*, kan de reactie webpagina's, nieuws, Video's en afbeeldingen bevatten.

### <a name="object-changes"></a>Object wijzigingen

- Het veld is toegevoegd `someResultsRemoved` aan het [webantwoord](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) -object. Het veld bevat een Booleaanse waarde die aangeeft of het antwoord een deel van de resultaten van het webantwoorden heeft uitgesloten.