---
title: Een basisquery maken
titleSuffix: Azure Cognitive Search
description: Meer informatie over het maken van een query aanvraag in Cognitive Search, welke hulpprogram ma's en Api's moeten worden gebruikt voor testen en code en hoe query beslissingen beginnen met het ontwerp van de index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 9bee391ddb0fa6c270c6d833fb7e81d5f4880497
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118639"
---
# <a name="create-a-query-in-azure-cognitive-search"></a>Een query maken in azure Cognitive Search

Als u een query voor de eerste keer bouwt, worden in dit artikel de hulpprogram ma's en Api's beschreven die u nodig hebt, welke methoden worden gebruikt voor het maken van een query en hoe index structuur en inhoud van invloed kunnen zijn op de resultaten van query's. Voor een inleiding op hoe een query-aanvraag eruitziet, begint u met [query typen en-samen stellingen](search-query-overview.md).

## <a name="choose-tools-and-apis"></a>Kies hulp middelen en Api's

U hebt een hulp programma of API nodig om een query te maken. Een van de volgende suggesties is handig voor het testen en uitvoeren van werk belastingen.

| Methodologie | Beschrijving |
|-------------|-------------|
| Portal| [Search Explorer (Portal)](search-explorer.md) is een query interface in de Azure portal die query's uitvoert op indexen voor de onderliggende zoek service. De portal maakt REST API aanroepen achter de schermen aan de bewerking [Zoeken in documenten](/rest/api/searchservice/search-documents) , maar kan geen automatisch aanvullen, suggesties of het opzoeken van documenten aanroepen.<br/><br/> U kunt een wille keurige index en REST API versie selecteren, inclusief preview. Een query reeks kan eenvoudige of volledige syntaxis gebruiken, met ondersteuning voor alle query parameters (filter, SELECT, searchFields, enzovoort). Wanneer u in de portal een index opent, kunt u met Search Explorer naast de definitie van de index-JSON in tabbladen naast elkaar werken voor eenvoudige toegang tot veld kenmerken. Controleer welke velden doorzoekbaar, sorteerbaar, filterbaar en bruikbaar zijn tijdens het testen van query's. <br/>Wordt aanbevolen voor vroegtijdig onderzoek, testen en validatie. [Meer informatie.](search-explorer.md) |
| Hulpprogram ma's voor webtest| [Postman](search-get-started-rest.md) of [Visual Studio code](search-get-started-vs-code.md) is een goede keuze voor het formuleren van een aanvraag voor een [Zoek opdracht](/rest/api/searchservice/search-documents) en een andere aanvraag, in rest. De REST Api's ondersteunen elke mogelijke programmatische bewerking in azure Cognitive Search en wanneer u een hulp programma zoals postman of Visual Studio code gebruikt, kunt u aanvragen interactief verlenen om te begrijpen hoe de functie werkt voordat u in code investeert. Een hulp programma voor het testen van webtoepassingen is een goede keuze als u geen Inzender of beheerders rechten hebt in de Azure Portal. Als u een zoek-URL en een query-API-sleutel hebt, kunt u de hulpprogram ma's gebruiken om query's uit te voeren op basis van een bestaande index. |
| Azure SDK | Wanneer u klaar bent voor het schrijven van code, kunt u de Azure.Search.Document-client bibliotheken gebruiken in de Azure-Sdk's voor .NET, Python, java script of Java. Elke SDK bevindt zich op een eigen release schema, maar u kunt al indexen maken en er query's op uitvoeren. <br/><br/>[SearchClient (.net)](/dotnet/api/azure.search.documents.searchclient) kan worden gebruikt voor het opvragen van een zoek index in C#.  [Meer informatie.](search-howto-dotnet-sdk.md)<br/><br/>[SearchClient (python)](/dotnet/api/azure.search.documents.searchclient) kan worden gebruikt om een zoek index in python op te vragen. [Meer informatie.](search-get-started-python.md)<br/><br/>[SearchClient (Java script)](/dotnet/api/azure.search.documents.searchclient) kan worden gebruikt voor het opvragen van een zoek index in Java script. [Meer informatie.](search-get-started-javascript.md) |

## <a name="set-up-a-search-client"></a>Een Search-client instellen

Een zoek-client verifieert de zoek service, verzendt aanvragen en verwerkt reacties. Ongeacht welk hulp programma of welke API u gebruikt, moet een zoek client het volgende hebben:

| Eigenschappen | Beschrijving |
|------------|-------------|
| Eindpunt | Een zoek service is URL-adresseerbaar in deze indeling: `https://[service-name].search.windows.net` . |
| API-toegangs sleutel (beheerder of query) | Verifieert de aanvraag bij de zoek service. |
| Index naam | Query's worden altijd door gestuurd bij de verzameling documenten van één index. U kunt geen indexen samen voegen of aangepaste of tijdelijke gegevens structuren maken als een query doel. |
| API-versie | Voor REST-aanroepen is de aanvraag expliciet vereist `api-version` . Client bibliotheken in de Azure SDK zijn daarentegen geversied op basis van een specifieke REST API versie. Voor Sdk's is de `api-version` impliciet. |

### <a name="in-the-portal"></a>In de portal

Search Explorer en andere portal-hulpprogram ma's hebben een ingebouwde client verbinding met de service, met directe toegangs indexen en andere objecten van portal-pagina's. Voor toegang tot hulpprogram ma's, wizards en objecten moet u lid zijn van de rol Inzender of hoger voor de service. 

### <a name="using-rest"></a>REST gebruiken

Voor REST-aanroepen kunt u [postman of soort gelijke hulp middelen](search-get-started-rest.md) gebruiken als de client om een aanvraag voor [Zoek documenten](/rest/api/searchservice/search-documents) op te geven. Elke aanvraag is zelfstandig, dus u moet het eind punt, de index naam en de API-versie op elke aanvraag opgeven. Andere eigenschappen, het inhouds type en de API-sleutel worden door gegeven aan de aanvraag header. 

U kunt POST gebruiken of ophalen om een index op te vragen. POST, met para meters die zijn opgegeven in de hoofd tekst van de aanvraag, is gemakkelijker te gebruiken. Als u POST gebruikt, zorg er dan voor dat u `docs/search` in de URL opgeeft:

```http
POST https://myservice.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

### <a name="using-azure-sdks"></a>Azure Sdk's gebruiken

Als u een Azure SDK gebruikt, maakt u de-client in code. Alle Sdk's bieden Zoek clients die de status kunnen behouden, waardoor verbinding opnieuw kan worden gemaakt. Voor query bewerkingen maakt u een instantie **`SearchClient`** en geeft u waarden op voor de volgende eigenschappen: eind punt, sleutel, index. Vervolgens kunt u de **`Search method`** door geven aanroepen in de query reeks. 

| Taal | Client | Voorbeeld |
|----------|--------|---------|
| C# en .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Uw eerste Zoek query in C verzenden #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Uw eerste Zoek query in python verzenden](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Uw eerste Zoek query verzenden in Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| Javascript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Uw eerste Zoek query verzenden in Java script](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Kies een parser: eenvoudig | waard

Als uw query zoeken in volledige tekst is, wordt er een parser gebruikt om de inhoud van de zoek parameter te verwerken. Azure Cognitive Search biedt twee query-parsers. De eenvoudige parser begrijpt de [eenvoudige query syntaxis](query-simple-syntax.md). Deze parser is geselecteerd als de standaard waarde voor de snelheid en effectiviteit in vrije-tekst query's. De syntaxis ondersteunt algemene zoek operatoren (en, of niet) voor Zoek opdrachten voor termen en zinsdelen, en voor voegsel ( `*` ) zoeken (zoals in "zee *" voor Seattle en Seaside). Een algemene aanbeveling is om eerst de eenvoudige parser te proberen en vervolgens over te stappen op volledige parser als aan toepassings vereisten wordt voldaan voor krachtigere query's.

De [volledige lucene-query syntaxis](query-Lucene-syntax.md#bkmk_syntax), ingeschakeld wanneer u `queryType=full` aan de aanvraag toevoegt, is gebaseerd op de [Apache Lucene-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

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

Als u de [grond beginselen van een query aanvraag](search-query-overview.md)eerder hebt bekeken, is het mogelijk dat de para meters in de query aanvraag afhankelijk zijn van de manier waarop velden in een index worden toegeschreven. Als u bijvoorbeeld wilt gebruiken in een query, filter of sorteer volgorde, moet een veld *doorzoekbaar*, *filterbaar* en *sorteerbaar* zijn. Op dezelfde manier kunnen alleen in de resultaten gemarkeerde *velden worden weer* gegeven. Wanneer u begint met het opgeven `search` `filter` `orderby` van de para meters,, en in uw aanvraag, moet u de kenmerken controleren terwijl u gaat om onverwachte resultaten te voor komen.

In de scherm afbeelding van de portal onder in de voor [beeld-index](search-get-started-portal.md)van de hotels kunnen alleen de laatste twee velden "LastRenovationDate" en "rating" worden gebruikt in een `"$orderby"` alleen-component.

![Index definitie voor het voor beeld van een hotel](./media/search-query-overview/hotel-sample-index-definition.png "Index definitie voor het voor beeld van een hotel")

Zie [Create Index (rest API)](/rest/api/searchservice/create-index)voor een beschrijving van veld kenmerken.

## <a name="know-your-tokens"></a>Ken uw tokens

Tijdens het indexeren gebruikt de query-engine een analyse programma voor het uitvoeren van tekst analyse op teken reeksen, waardoor het mogelijk is om op de query tijd te zoeken. Teken reeksen zijn mini maal in kleine letters, maar kunnen ook lemmatisering en het verwijderen van woorden worden gestopt. Grotere teken reeksen of samengestelde woorden worden meestal gesplitst door witruimte, afbreek streepjes of streepjes, en geïndexeerd als afzonderlijke tokens. 

Het punt om dit te doen, is dat uw index er als volgt uitziet: wat u in werkelijkheid vindt, en wat u erin kunt onderscheiden. Als query's geen verwachte resultaten retour neren, kunt u de tokens die door de analyse zijn gemaakt, inspecteren via de [analyse tekst (rest API)](/rest/api/searchservice/test-analyzer). Zie voor meer informatie over het door geven van de tokens en de invloed op query's [gedeeltelijke term zoeken en patronen met speciale tekens](search-query-partial-matching.md).

## <a name="next-steps"></a>Volgende stappen

Nu u een beter inzicht hebt in de manier waarop een query aanvraag is samengesteld, kunt u de volgende Snelstartgids volgen voor een praktijk ervaring.

+ [Zoek Verkenner](search-explorer.md)
+ [Query's uitvoeren in REST](search-get-started-rest.md)
+ [Query's uitvoeren in .NET](search-get-started-dotnet.md)
+ [Query's uitvoeren in python](search-get-started-python.md)
+ [Query's uitvoeren in Java script](search-get-started-javascript.md)