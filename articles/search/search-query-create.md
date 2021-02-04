---
title: Een query maken
titleSuffix: Azure Cognitive Search
description: Meer informatie over het maken van een query aanvraag in Cognitive Search, welke hulpprogram ma's en Api's moeten worden gebruikt voor testen en code en hoe query beslissingen beginnen met het ontwerp van de index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: b013c66feefade077c85194ba3b1ff04ff4c4aa5
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99536829"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Query's maken in azure Cognitive Search

Als u een query voor de eerste keer bouwt, worden in dit artikel benaderingen en methoden beschreven voor het instellen van query's. Er wordt ook een query aanvraag geïntroduceerd en wordt uitgelegd hoe veld kenmerken en taal kundige analysen de query resultaten kunnen beïnvloeden.

## <a name="whats-a-query-request"></a>Wat is een query aanvraag?

Een query is een alleen-lezen aanvraag voor de docs-verzameling van een enkele zoek index. Er wordt een ' query type ' en een query-expressie opgegeven, hoewel de para meter ' Search ' is. De query-expressie kan zoek termen, een gegroepeerde aanhalings tekens en Opera tors bevatten.

Een query kan ook ' count ' bevatten om het aantal gevonden overeenkomsten in de index te retour neren, ' Select ' selecteren om te kiezen welke velden worden geretourneerd in het Zoek resultaat en ' OrderBy ' om de resultaten te sorteren. In het volgende voor beeld krijgt u een algemeen idee van een query aanvraag door een subset van de beschik bare para meters weer te geven. Zie [query typen en-samen stellingen](search-query-overview.md) en [documenten zoeken (rest)](/rest/api/searchservice/search-documents)voor meer informatie over het samen stellen van query's.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
    "search": "`New York` +restaurant",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "count": "true",
    "orderby": "Rating desc"
}
```

## <a name="choose-a-client"></a>Kies een client

U hebt een hulp programma nodig zoals Azure Portal of Postman, of code waarmee een query-client met Api's wordt geïnstantieerd. We raden u aan de Azure Portal-of REST-Api's voor vroegtijdige ontwikkeling en test-of-concept tests uit te voeren.

### <a name="permissions"></a>Machtigingen

Elke bewerking, inclusief query aanvragen, werkt onder een [beheer-API-sleutel](search-security-api-keys.md), maar query aanvragen kunnen eventueel gebruikmaken van een query-API- [sleutel](search-security-api-keys.md#create-query-keys). Query-API-sleutels worden nadrukkelijk aanbevolen. U kunt Maxi maal 50 per service maken en verschillende sleutels toewijzen aan verschillende toepassingen.

In Azure Portal is voor toegang tot hulp middelen, wizards en objecten lidmaatschap van de rol Inzender of hoger vereist voor de service. 

### <a name="use-azure-portal-to-query-an-index"></a>Azure Portal gebruiken om een index op te vragen

[Search Explorer (Portal)](search-explorer.md) is een query interface in de Azure portal die query's uitvoert op indexen voor de onderliggende zoek service. Intern maakt de portal [Zoek opdrachten naar](/rest/api/searchservice/search-documents) aanvragen, maar kan AutoAanvullen, suggesties of het opzoeken van documenten niet aanroepen. 

U kunt een wille keurige index en REST API versie selecteren, inclusief preview. Een query reeks kan eenvoudige of volledige syntaxis gebruiken, met ondersteuning voor alle query parameters (filter, SELECT, searchFields, enzovoort). Wanneer u in de portal een index opent, kunt u met Search Explorer naast de definitie van de index-JSON in tabbladen naast elkaar werken voor eenvoudige toegang tot veld kenmerken. Controleer welke velden doorzoekbaar, sorteerbaar, filterbaar en bruikbaar zijn tijdens het testen van query's.

### <a name="use-a-rest-client"></a>Een REST-client gebruiken

Postman en Visual Studio code (met een extensie voor Azure Cognitive Search) kunnen worden gebruikt als een query-client. Met beide hulp middelen kunt u verbinding maken met uw zoek service en aanvragen voor [Zoek documenten (rest)](/rest/api/searchservice/search-documents) verzenden. Talloze zelf studies en voor beelden illustreren REST-clients voor het uitvoeren van query's op indexering. 

Begin met een van deze artikelen voor meer informatie over elke client (neem instructies voor query's op):

+ [Een zoek index maken met behulp van REST en postman](search-get-started-rest.md)
+ [Aan de slag met Visual Studio Code en Azure Cognitive Search](search-get-started-vs-code.md)

Elke aanvraag is zelfstandig, dus u moet het eind punt, de index naam en de API-versie op elke aanvraag opgeven. Andere eigenschappen, het inhouds type en de API-sleutel worden door gegeven aan de aanvraag header. Zie [documenten zoeken (rest)](/rest/api/searchservice/search-documents) voor hulp bij het formuleren van query-aanvragen voor meer informatie.

### <a name="use-an-sdk"></a>Een SDK gebruiken

Voor Cognitive Search implementeren de Azure-Sdk's algemeen beschik bare functies. Zo kunt u een van de Sdk's gebruiken om een index op te vragen. Allemaal bieden een **SearchClient** die methoden heeft om te communiceren met een index, van het laden van een index met Zoek documenten om query aanvragen te formuleren.

| Azure SDK | Client | Voorbeelden |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample. java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| Javascript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [readonlyQuery.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query. py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>Kies een query type: eenvoudig | waard

Als uw query zoeken in volledige tekst is, wordt een query-parser gebruikt voor het verwerken van de tekst die wordt door gegeven als zoek termen en zinsdelen. Azure Cognitive Search biedt twee query-parsers. 

+ De eenvoudige parser begrijpt de [eenvoudige query syntaxis](query-simple-syntax.md). Deze parser is geselecteerd als de standaard waarde voor de snelheid en effectiviteit in vrije-tekst query's. De syntaxis ondersteunt algemene zoek operatoren (en, of niet) voor Zoek opdrachten voor termen en zinsdelen, en voor voegsel ( `*` ) zoeken (zoals in "zee *" voor Seattle en Seaside). Een algemene aanbeveling is om eerst de eenvoudige parser te proberen en vervolgens over te stappen op volledige parser als aan toepassings vereisten wordt voldaan voor krachtigere query's.

+ De [volledige lucene-query syntaxis](query-Lucene-syntax.md#bkmk_syntax), ingeschakeld wanneer u `queryType=full` aan de aanvraag toevoegt, is gebaseerd op de [Apache Lucene-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

De volledige syntaxis en eenvoudige syntaxis overlappen elkaar in zoverre beide dezelfde voor voegsel-en Booleaanse bewerkingen ondersteunen, maar de volledige syntaxis biedt meer Opera tors. Volledig zijn er meer Opera tors voor Boole-expressies en meer Opera tors voor geavanceerde query's, zoals fuzzy zoeken, joker tekens zoeken, Zoek opdrachten op nabijheid en reguliere expressies.

## <a name="choose-query-methods"></a>Query methoden kiezen

Zoeken is fundamenteel een door de gebruiker opgestuurde oefening, waarbij de termen of zinsdelen worden verzameld uit een zoekvak, of vanuit klikken op gebeurtenissen op een pagina. De volgende tabel bevat een overzicht van de mechanismen waarmee u gebruikers invoer kunt verzamelen, samen met de verwachte Zoek ervaring.

| Invoer | Ervaring |
|-------|---------|
| [Zoek methode](/rest/api/searchservice/search-documents) | Een gebruiker typt termen of zinsdelen in een zoekvak, met of zonder Opera Tors, en klikt op zoeken om de aanvraag te verzenden. Search kan worden gebruikt met filters voor dezelfde aanvraag, maar niet met automatisch aanvullen of suggesties. |
| [Methode voor automatisch aanvullen](/rest/api/searchservice/autocomplete) | Een gebruiker kan een paar tekens typen en er worden query's gestart nadat elk nieuw teken is getypt. Het antwoord is een voltooide teken reeks uit de index. Als de gegeven teken reeks geldig is, klikt de gebruiker op zoeken om die query naar de service te verzenden. |
| [Suggesties, methode](/rest/api/searchservice/suggestions) | Net als bij automatisch aanvullen worden een aantal tekens en incrementele query's gegenereerd. Het antwoord is een vervolg keuzelijst met overeenkomende documenten, meestal vertegenwoordigd door een paar unieke of beschrijvende velden. Als een van de selecties geldig is, klikt de gebruiker op één en wordt het bijbehorende document geretourneerd. |
| [Facetnavigatie](/rest/api/searchservice/search-documents#query-parameters) | Een pagina bevat klik bare navigatie koppelingen of brood kruimels waarmee het bereik van de zoek opdracht wordt beperkt. Een facet navigatie structuur is dynamisch samengesteld op basis van een eerste query. `search=*`Als u bijvoorbeeld een facet navigatie structuur wilt vullen die bestaat uit elke mogelijke categorie. Een facet navigatie structuur wordt gemaakt op basis van een query-antwoord, maar is ook een mechanisme voor het uitdrukken van de volgende query. n REST API verwijzing, `facets` wordt gedocumenteerd als een query parameter van een zoek bewerking naar een document, maar kan zonder de `search` para meter worden gebruikt.|
| [Filter methode](/rest/api/searchservice/search-documents#query-parameters) | Filters worden gebruikt met facetten om de resultaten te beperken. U kunt ook een filter achter de pagina implementeren, bijvoorbeeld om de pagina te initialiseren met taalspecifieke velden. In REST API verwijzing `$filter` wordt beschreven als een query parameter van een zoek bewerking naar een document, maar kan zonder de `search` para meter worden gebruikt.|

## <a name="know-your-field-attributes"></a>Ken uw veld kenmerken

Als u eerder [query typen en samen stelling](search-query-overview.md)hebt gecontroleerd, is het mogelijk dat de para meters in de query aanvraag afhankelijk zijn van de manier waarop velden in een index worden toegeschreven. Als u bijvoorbeeld wilt gebruiken in een query, filter of sorteer volgorde, moet een veld *doorzoekbaar*, *filterbaar* en *sorteerbaar* zijn. Op dezelfde manier kunnen alleen in de resultaten gemarkeerde *velden worden weer* gegeven. Wanneer u begint met het opgeven `search` `filter` `orderby` van de para meters,, en in uw aanvraag, moet u de kenmerken controleren terwijl u gaat om onverwachte resultaten te voor komen.

In de scherm afbeelding van de portal onder in de voor [beeld-index](search-get-started-portal.md)van de hotels kunnen alleen de laatste twee velden "LastRenovationDate" en "rating" worden gebruikt in een `"$orderby"` alleen-component.

![Index definitie voor het voor beeld van een hotel](./media/search-query-overview/hotel-sample-index-definition.png "Index definitie voor het voor beeld van een hotel")

Zie [Create Index (rest API)](/rest/api/searchservice/create-index)voor een beschrijving van veld kenmerken.

## <a name="know-your-tokens"></a>Ken uw tokens

Tijdens het indexeren gebruikt de zoek machine een analyse programma voor het uitvoeren van tekst analyse op teken reeksen, waardoor het mogelijk is om op query tijd te zoeken. Teken reeksen zijn mini maal in kleine letters, maar kunnen ook lemmatisering en het verwijderen van woorden worden gestopt. Grotere teken reeksen of samengestelde woorden worden meestal gesplitst door witruimte, afbreek streepjes of streepjes, en geïndexeerd als afzonderlijke tokens. 

Het punt om dit te doen, is dat uw index er als volgt uitziet: wat u in werkelijkheid vindt, en wat u erin kunt onderscheiden. Als query's geen verwachte resultaten retour neren, kunt u de tokens die door de analyse zijn gemaakt, inspecteren via de [analyse tekst (rest API)](/rest/api/searchservice/test-analyzer). Zie voor meer informatie over het door geven van de tokens en de invloed op query's [gedeeltelijke term zoeken en patronen met speciale tekens](search-query-partial-matching.md).

## <a name="next-steps"></a>Volgende stappen

Nu u een beter inzicht hebt in hoe query aanvragen werken, kunt u de volgende Snelstartgids gebruiken om praktijk ervaring te ondervinden.

+ [Zoek Verkenner](search-explorer.md)
+ [Query's uitvoeren in REST](search-get-started-rest.md)
+ [Query's uitvoeren in .NET](search-get-started-dotnet.md)
+ [Query's uitvoeren in python](search-get-started-python.md)
+ [Query's uitvoeren in Java script](search-get-started-javascript.md)