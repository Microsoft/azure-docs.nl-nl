---
title: Querytypen en querysamenstelling
titleSuffix: Azure Cognitive Search
description: Basis principes voor het bouwen van een zoek query in azure Cognitive Search, met behulp van para meters voor het filteren, selecteren en sorteren van resultaten.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 362f46290bbe2008f9fb862a8711577050050192
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693246"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Query typen en samen stelling in azure Cognitive Search

In azure Cognitive Search is een query een volledige specificatie van een round-trip bewerking. Op de aanvraag zijn er para meters die uitvoerings instructies bieden voor de engine, evenals de para meters die het antwoord vormen. Niet opgegeven ( `search=*` ), zonder match criteria en het gebruik van null of standaard parameters, wordt een query uitgevoerd voor alle Doorzoek bare velden als een zoek bewerking in volledige tekst, waardoor een oneindige resultatenset in een wille keurige volg orde wordt geretourneerd.

Het volgende voor beeld is een representatieve query die is geconstrueerd in de [rest API](/rest/api/searchservice/search-documents). In dit voor beeld wordt de index voor de [Hotels-demo](search-get-started-portal.md) en de algemene para meters opgenomen, zodat u kunt zien hoe een query eruit ziet.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** Hiermee stelt u de parser in, ofwel de [standaard-query-parser](search-query-simple-examples.md) (optimaal voor zoeken in volledige tekst), of de [volledige lucene-query-parser](search-query-lucene-examples.md) die wordt gebruikt voor geavanceerde query constructies, zoals reguliere expressies, proximity Search, fuzzy en Joker tekens zoeken.

+ **`search`** biedt de match criteria, meestal hele termen of zinsdelen, maar vaak vergezeld van Booleaanse Opera tors. Enkele zelfstandige termen zijn *term* query's. Query's met meerdere delen van citaten zijn *woordgroepen* query's. De zoek opdracht kan niet worden gedefinieerd, zoals in **`search=*`** , maar er is geen criteria om op te zoeken. de resultatenset bestaat uit wille keurig geselecteerde documenten.

+ **`searchFields`** beperkt de uitvoering van query's naar specifieke velden. Elk veld waarvoor een *zoekbaar* kenmerk is in het index schema, is een kandidaat voor deze para meter.

Antwoorden worden ook gevormd door de para meters die u opneemt in de query:

+ **`select`** geeft aan welke velden moeten worden geretourneerd in het antwoord. Alleen velden die zijn gemarkeerd als *ophalen* in de index kunnen worden gebruikt in een SELECT-instructie.

+ **`top`** retourneert het opgegeven aantal best overeenkomende documenten. In dit voor beeld worden slechts tien hits geretourneerd. Als u de resultaten wilt weer geven, kunt u met de bovenkant en de pagina overs Laan.

+ **`count`** geeft aan hoeveel documenten in de volledige index in zijn geheel overeenkomen, wat meer kan zijn dan wat er wordt geretourneerd. 

+ **`orderby`** wordt gebruikt als u resultaten wilt sorteren op een waarde, zoals een classificatie of locatie. Anders is de standaard waarde het gebruik van de relevantie score om de resultaten te classificeren.

In azure Cognitive Search is het uitvoeren van query's altijd op basis van één index, geauthenticeerd met een API-sleutel die in de aanvraag is opgenomen. In REST worden beide weer gegeven in aanvraag headers.

### <a name="how-to-run-this-query"></a>Deze query uitvoeren

Voordat u code schrijft, kunt u query hulpprogramma's gebruiken om te zien wat de syntaxis is en experimenteren met verschillende para meters. De snelste benadering is het ingebouwde Portal-hulp programma, [Search Explorer](search-explorer.md).

Als u deze Quick Start hebt uitgevoerd [om de hotels-demo-index te maken](search-get-started-portal.md), kunt u deze query reeks plakken in de zoek balk van de Explorer om uw eerste query uit te voeren: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Hoe query bewerkingen worden ingeschakeld door de index

Het ontwerp van de index en het query ontwerp zijn nauw verbonden met Azure Cognitive Search. Een essentieel feit dat u vooraf moet weten, is dat het *index schema*, met kenmerken voor elk veld, bepaalt welk soort query u kunt maken. 

Index kenmerken voor een veld stel de toegestane bewerkingen in: Hiermee stelt u in dat een veld in de index kan worden *doorzocht* en kan worden *opgehaald* , *gesorteerd*, *gefilterd*, enzovoort. In de voorbeeld query teken reeks `"$orderby": "Rating"` werkt alleen omdat het classificatie veld is gemarkeerd als *sorteerbaar* in het index schema. 

![Index definitie voor het voor beeld van een hotel](./media/search-query-overview/hotel-sample-index-definition.png "Index definitie voor het voor beeld van een hotel")

De bovenstaande scherm afbeelding is een gedeeltelijke lijst met index kenmerken voor het voor beeld van hotels. U kunt het volledige index schema weer geven in de portal. Zie [Create index rest API](/rest/api/searchservice/create-index)voor meer informatie over index kenmerken.

> [!Note]
> Sommige query functies zijn in index-breed en niet per veld. Deze mogelijkheden omvatten: [synoniemen](search-synonyms.md), [aangepaste analyse](index-add-custom-analyzers.md)functies, [suggestie constructies (voor automatisch aanvullen en voorgestelde query's)](index-add-suggesters.md), [Score logica voor classificatie resultaten](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementen van een query aanvraag

Query's zijn altijd gericht op één index. U kunt geen indexen samen voegen of aangepaste of tijdelijke gegevens structuren maken als een query doel. 

Vereiste elementen voor een query aanvraag zijn onder andere de volgende onderdelen:

+ Verzameling service-eind punten en index documenten, uitgedrukt als een URL met vaste en door de gebruiker gedefinieerde onderdelen: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Alleen REST) is vereist omdat meer dan één versie van de API te allen tijde beschikbaar is. 
+ **`api-key`**, ofwel een query of beheerder-API-sleutel, verifieert de aanvraag bij uw service.
+ **`queryType`**, eenvoudig of volledig, wat kan worden wegge laten als u de ingebouwde standaard eenvoudige syntaxis gebruikt.
+ **`search`** of **`filter`** biedt de overeenkomende criteria, die niet kunnen worden opgegeven als u een lege zoek opdracht wilt uitvoeren. Beide query typen worden besproken in termen van de eenvoudige parser, maar zelfs geavanceerde query's vereisen de zoek parameter voor het door geven van complexe query expressies.

Alle andere zoek parameters zijn optioneel. Zie [Create Index (rest) (Engelstalig)](/rest/api/searchservice/create-index)voor een volledige lijst met kenmerken. Zie [Hoe zoeken in volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)voor meer informatie over hoe para meters worden gebruikt tijdens de verwerking.

## <a name="choose-apis-and-tools"></a>Kies Api's en hulpprogram ma's

De volgende tabel geeft een lijst van de Api's en hulp op basis van gereedschappen voor het verzenden van query's.

| Methodologie | Beschrijving |
|-------------|-------------|
| [Search Explorer (portal)](search-explorer.md) | Voorziet in een zoek balk en opties voor selecties van index en API-versie. Resultaten worden geretourneerd als JSON-documenten. Aanbevolen voor verkennen, testen en valideren. <br/>[Meer informatie.](search-get-started-portal.md#query-index) | 
| [Postman of andere REST-hulp middelen](search-get-started-rest.md) | Webtest-hulpprogram ma's zijn een uitstekende keuze voor het formuleren van REST-aanroepen. De REST API ondersteunt elke mogelijke bewerking in azure Cognitive Search. In dit artikel vindt u informatie over het instellen van een HTTP-aanvraag header en hoofd tekst voor het verzenden van aanvragen naar Azure Cognitive Search.  |
| [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) | Client die kan worden gebruikt om een query uit te zoeken op een Azure Cognitive Search-index.  <br/>[Meer informatie.](search-howto-dotnet-sdk.md)  |
| [Documenten zoeken (REST API)](/rest/api/searchservice/search-documents) | GET-of POST-methoden voor een index met behulp van query parameters voor aanvullende invoer.  |

## <a name="choose-a-parser-simple--full"></a>Kies een parser: eenvoudig | waard

Azure Cognitive Search bevindt zich boven Apache Lucene en biedt u een keuze tussen twee query-parsers voor het verwerken van typische en gespecialiseerde query's. Aanvragen die gebruikmaken van de eenvoudige parser worden geformuleerd met behulp van de [eenvoudige query syntaxis](query-simple-syntax.md), geselecteerd als de standaard waarde voor de snelheid en effectiviteit in vrije-tekst query's. Deze syntaxis ondersteunt een aantal veelgebruikte Zoek operatoren, waaronder de Opera tors en, of, niet, frase, achtervoegsel en voor rang.

De [volledige lucene-query syntaxis](query-Lucene-syntax.md#bkmk_syntax), ingeschakeld wanneer u `queryType=full` aan de aanvraag toevoegt, geeft de uitgebreide en duidelijke query taal weer die is ontwikkeld als onderdeel van [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). De volledige syntaxis breidt de eenvoudige syntaxis uit. Alle query's die u voor de eenvoudige syntaxis schrijft, worden uitgevoerd onder de volledige lucene-parser. 

In de volgende voor beelden ziet u het punt: dezelfde query, maar met verschillende query type-instellingen kunt u verschillende resultaten opleveren. In de eerste query wordt de `^3` After `historic` beschouwd als onderdeel van de zoek term. Het hoogste geclassificeerde resultaat voor deze query is "Marquis Plaza & Suites", die *Oceaan* in de beschrijving heeft

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Dezelfde query die gebruikmaakt van de volledige lucene-parser, interpreteert `^3` als een in-Field Booster. Switch parsers wijzigen de positie, met de resultaten met de term *historisch* naar boven.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typen query's

Azure Cognitive Search ondersteunt een breed scala aan query typen. 

| Query type | Gebruik | Voor beelden en meer informatie |
|------------|--------|-------------------------------|
| Zoek opdracht in vrije tekst | Zoek parameter en beide parsers| Zoek opdrachten in volledige tekst scans voor een of meer voor waarden in alle *Doorzoek* bare velden in uw index en werkt op de manier waarop u een zoek machine zoals Google of Bing verwacht. Het voor beeld in de inleiding is zoeken in volledige tekst.<br/><br/>Zoek opdracht in volledige tekst ondergaate lexicale analyse met behulp van de standaard-lucene Analyzer (standaard) om alle voor waarden kleine letters te verlagen, verwijder stop woorden zoals ' de '. U kunt de standaard instelling overschrijven met [niet-Engelse analyse](index-add-language-analyzers.md#language-analyzer-list) functies of [gespecialiseerde neutraal-](index-add-custom-analyzers.md#AnalyzerTable) analyse functies waarmee lexicale analyses worden gewijzigd. Een voor beeld is een [tref woord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) dat de volledige inhoud van een veld als één token behandelt. Dit is handig voor gegevens zoals post codes, Id's en sommige product namen. | 
| Gefilterde zoek opdracht | [OData-filter expressie](query-odata-filter-orderby-syntax.md) en een van beide parsers | Met filter query's wordt een booleaanse expressie voor alle *filter* bare velden in een index geëvalueerd. In tegens telling tot zoeken komt een filter query overeen met de exacte inhoud van een veld, inclusief hoofdletter gevoeligheid voor teken reeks velden. Een ander verschil is dat filter query's worden weer gegeven in de OData-syntaxis. <br/>[Voor beeld van filter expressie](search-query-simple-examples.md#example-3-filter-queries) |
| Op geografische locaties zoeken | Het [type EDM. GeographyPoint](/rest/api/searchservice/supported-data-types) in het veld, filter expressie en beide parsers | Coördinaten die zijn opgeslagen in een veld met een EDM. GeographyPoint worden gebruikt voor de besturings elementen "dichtbij zoeken" of op basis van een kaart. <br/>[Voor beeld van geo-zoeken](search-query-simple-examples.md#example-5-geo-search)|
| Bereik zoeken | filter expressie en eenvoudige parser | In azure Cognitive Search worden bereik query's gemaakt met behulp van de filter parameter. <br/>[Voor beeld van Range-filter](search-query-simple-examples.md#example-4-range-filters) | 
| [Zoek opdracht in veld](query-lucene-syntax.md#bkmk_fields) | Zoek parameter en volledige parser | Bouw een samengestelde query-expressie die is gericht op één veld. <br/>[Voor beeld van een zoek opdracht naar een veld](search-query-lucene-examples.md#example-2-fielded-search) |
| [Zoek actie op fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | Zoek parameter en volledige parser | Komt overeen met de termen met een vergelijk bare constructie of spelling. <br/>[Voor beeld van fuzzy zoeken](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [Proximity Search](query-lucene-syntax.md#bkmk_proximity) | Zoek parameter en volledige parser | Hiermee worden zoek termen in een document in de buurt. <br/>[Voor beeld van proximity Search](search-query-lucene-examples.md#example-4-proximity-search) |
| [term versterking](query-lucene-syntax.md#bkmk_termboost) | Zoek parameter en volledige parser | Hiermee wordt een document hoger gerangschikt als het de gestimuleerde term bevat, ten opzichte van andere. <br/>[Voor beeld van een betere term](search-query-lucene-examples.md#example-5-term-boosting) |
| [reguliere expressie zoeken](query-lucene-syntax.md#bkmk_regex) | Zoek parameter en volledige parser | Komt overeen met de inhoud van een reguliere expressie. <br/>[Voor beeld van een reguliere expressie](search-query-lucene-examples.md#example-6-regex) |
|  [Joker teken of voor voegsel zoeken](query-lucene-syntax.md#bkmk_wildcard) | Zoek parameter en volledige parser | Komt overeen met een voor voegsel en tilde () `~` of één teken ( `?` ). <br/>[Zoek voorbeeld voor joker tekens](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Zoek resultaten beheren 

Query resultaten worden gestreamd als JSON-documenten in de REST API, hoewel u .NET Api's gebruikt, is serialisatie ingebouwd. U kunt de resultaten van de vorm bepalen door para meters in te stellen voor de query en specifieke velden te selecteren voor het antwoord.

De para meters in de query kunnen worden gebruikt om de resultatenset op de volgende manieren te structureren:

+ Het aantal documenten in de resultaten beperken of batcheren (standaard 50)
+ Velden selecteren die in de resultaten moeten worden meegenomen
+ Een sorteer volgorde instellen
+ Treffers toevoegen om de aandacht te vestigen op overeenkomende voor waarden in de hoofd tekst van de zoek resultaten

### <a name="tips-for-unexpected-results"></a>Tips voor onverwachte resultaten

Af en toe zijn de stoffen en niet de structuur van de resultaten onverwacht. Wanneer de resultaten van de query niet naar verwachting worden weer geven, kunt u deze query wijzigingen proberen om te zien of het resultaat wordt verbeterd:

+ Wijzig **`searchMode=any`** (standaard) in **`searchMode=all`** om overeenkomsten te vereisen voor alle criteria in plaats van een criterium. Dit geldt met name wanneer Boole-Opera tors worden opgenomen in de query.

+ Wijzig de query techniek als tekst-of lexicale analyse nood zakelijk is, maar het query type belet taal verwerking. In zoeken in volledige tekst, tekst-of lexicale analyse auto correctie voor spel fouten, enkelvoudige Word-formulieren en zelfs onregelmatige woorden of zelfstandige naam woorden. Voor sommige query's, zoals fuzzy of joker tekens zoeken, maakt lexicale analyse geen deel uit van de pipeline voor het parseren van query's. In sommige gevallen worden reguliere expressies gebruikt als tijdelijke oplossing. 

### <a name="paging-results"></a>Resultaten pagineren
Met Azure Cognitive Search kunt u eenvoudig paginering van zoek resultaten implementeren. Met behulp van de **`top`** **`skip`** -en-para meters kunt u probleemloos Zoek opdrachten verzenden waarmee u de totale set Zoek resultaten in beheerbaar, bestelde subsets waarmee u eenvoudig goede zoek acties in de gebruikers interface kunt kunnen ontvangen. Tijdens het ontvangen van deze kleinere subsets van resultaten kunt u ook het aantal documenten weergeven in het totale aantal zoekresultaten.

Meer informatie over het pagineren van zoek resultaten vindt u in het artikel de [pagina met zoek resultaten in Azure Cognitive Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Resultaten ordenen
Wanneer er resultaten voor een zoek query worden ontvangen, kunt u aanvragen dat Azure Cognitive Search de resultaten bedient die zijn gesorteerd op waarden in een specifiek veld. Standaard worden de zoek resultaten door Azure Cognitive Search gesorteerd op basis van de positie van de zoek Score van elk document, die is afgeleid van [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Als u wilt dat Azure Cognitive Search uw resultaten retourneert die zijn besteld met een andere waarde dan de zoek Score, kunt u de **`orderby`** Zoek parameter gebruiken. U kunt de waarde van de para meter opgeven voor het **`orderby`** toevoegen van veld namen en aanroepen naar de [**`geo.distance()` functie**](query-odata-filter-orderby-syntax.md) voor georuimtelijke waarden. Elke expressie kan worden gevolgd door `asc` om aan te geven dat de resultaten in oplopende volg orde worden aangevraagd en **`desc`** om aan te geven dat de resultaten in aflopende volg orde worden aangevraagd. De standaard oplopende volgorde.


### <a name="hit-highlighting"></a>Markeren
In azure Cognitive Search is het benadrukken van het exacte deel van de zoek resultaten dat overeenkomt met de zoek query, eenvoudig gemaakt met behulp van de **`highlight`** **`highlightPreTag`** **`highlightPostTag`** para meters, en. U kunt opgeven in welke *Doorzoek* bare velden de overeenkomende tekst moet worden benadrukt, evenals de exacte teken reeks tags die moeten worden toegevoegd aan het begin en het einde van de overeenkomende tekst die Azure Cognitive Search retourneert.

## <a name="see-also"></a>Zie ook

+ [De manier waarop zoeken in volledige tekst werkt in azure Cognitive Search (architectuur voor het parseren van query's)](search-lucene-query-architecture.md)
+ [Zoek Verkenner](search-explorer.md)
+ [Query's uitvoeren in .NET](./search-get-started-dotnet.md)
+ [Query's uitvoeren in REST](./search-get-started-powershell.md)