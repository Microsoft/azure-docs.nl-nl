---
title: Upgrade van Bing Afbeeldingen zoeken-API v5 naar v7
titleSuffix: Azure Cognitive Services
description: In deze upgrade handleiding worden de wijzigingen tussen versie 5 en versie 7 van de Bing Afbeeldingen zoeken-API beschreven. Gebruik deze hand leiding om u te helpen bij het identificeren van de onderdelen van uw toepassing die u moet bijwerken om versie 7 te gebruiken.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d10db37934bb0d6571eb0191d5f1be47dae000ed
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342172"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Upgrade handleiding voor Bing Afbeeldingen zoeken-API V7

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Deze upgrade handleiding bevat de wijzigingen tussen versie 5 en versie 7 van de Bing Afbeeldingen zoeken-API. Gebruik deze hand leiding om u te helpen bij het identificeren van de onderdelen van uw toepassing die u moet bijwerken om versie 7 te gebruiken.

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

### <a name="endpoints"></a>Eindpunten

- Het versie nummer van het eind punt is gewijzigd van v5 naar v7. Bijvoorbeeld https: \/ /API.Cognitive.Microsoft.com/Bing/ \* \* v 7.0 * */Images/Search.

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



### <a name="query-parameters"></a>Queryparameters

- De naam van de `modulesRequested` query parameter is gewijzigd in [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- De naam van de aantekeningen in Tags is gewijzigd. Zie [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) query parameter naar Tags.  

- De lijst met ondersteunde markten van de ShoppingSources-filter waarde is gewijzigd in alleen en-US. Zie [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Wijzigingen in image Insights

- De naam van het `annotations` veld van [ImagesInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) is gewijzigd in `imageTags` .  

- De naam van het `AnnotationModule` object is gewijzigd in [ImageTagsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- De naam van het `Annotation` object is gewijzigd in [Label](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag)en het `confidence` veld is verwijderd.  

- De naam van het `insightsSourcesSummary` veld van het [afbeeldings](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) object is gewijzigd in `insightsMetadata` .  

- De naam van het `InsightsSourcesSummary` object is gewijzigd in [InsightsMetadata](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Het `https://api.cognitive.microsoft.com/bing/v7.0/images/details` eind punt is toegevoegd. Gebruik dit eind punt voor het aanvragen van image Insights in plaats van het/Images/Search-eind punt. Zie [Image Insights](./image-insights.md).

- De volgende query parameters zijn nu alleen geldig voor het `/images/details` eind punt.  

    -   [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [-](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [overeenkomst](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [Cat5](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [CT](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- De naam van het `ImageInsightsResponse` object is gewijzigd in [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- De gegevens typen van de volgende velden in het [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) -object zijn gewijzigd.  

    -   Wijzig het type van het `relatedCollections` veld van `ImageGallery[]` in [RelatedCollectionsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Wijzig het type van het `pagesIncluding` veld van `Image[]` in [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Wijzig het type van het `relatedSearches` veld van `Query[]` in [RelatedSearchesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Wijzig het type van het `recipes` veld van `Recipe[]` in [RecipesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Wijzig het type van het `visuallySimilarImages` veld van `Image[]` in [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Wijzig het type van het `visuallySimilarProducts` veld van `ProductSummaryImage[]` in [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Het object is verwijderd `ProductSummaryImage` en de productgerelateerde velden zijn verplaatst naar het [afbeeldings](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) object. Het `Image` object bevat alleen de productgerelateerde velden wanneer de afbeelding is opgenomen als onderdeel van visueel vergelijk bare producten in een image Insight-reactie.  

    -   Wijzig het type van het `recognizedEntityGroups` veld van `RecognizedEntityGroup[]` in [RecognizedEntitiesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   De naam van het `categoryClassification` veld van [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) `annotations` is gewijzigd in `AnnotationsModule` .  

### <a name="images-answer"></a>Antwoord op installatie kopieën

-   De velden displayShoppingSourcesBadges en displayRecipeSourcesBadges zijn verwijderd uit [afbeeldingen](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   De naam van het `nextOffsetAddCount` veld met [installatie kopieën](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) is gewijzigd in `nextOffset` . De manier waarop u de offset gebruikt, is ook gewijzigd. Voorheen stelt u de [Offset](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) query-para meter in op de `nextOffsetAddCount` waarde plus de vorige offset waarde plus het aantal afbeeldingen in het resultaat. Nu kunt u `offset` de waarde instellen `nextOffset` .  


## <a name="non-breaking-changes"></a>Niet-brekende wijzigingen

### <a name="query-parameters"></a>Queryparameters

- Transparant als mogelijke [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) -filter waarde toegevoegd. Het transparante filter retourneert alleen afbeeldingen met een transparante achtergrond.

- De waarde van een mogelijke [licentie](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) filter is toegevoegd. Met elk filter worden alleen installatie kopieën geretourneerd die onder licentie vallen.

- De query parameters [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) en [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) zijn toegevoegd. Gebruik deze filters om installatie kopieën te retour neren binnen een bereik van bestands grootten.  

- De query parameters [maxHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [MaxWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth)en [minWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) zijn toegevoegd. Gebruik deze filters om afbeeldingen te retour neren binnen een bereik van hoogten en breedten.  

### <a name="object-changes"></a>Object wijzigingen

- De `description` velden en zijn toegevoegd `lastUpdated` aan het [aanbiedings](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) object.  

- Het veld is toegevoegd `name` aan het [ImageGallery](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery) -object.  

- Wordt toegevoegd `similarTerms` aan het [afbeeldingen](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) -object. Dit veld bevat een lijst met termen die vergelijkbaar zijn met de query teken reeks van de gebruiker.