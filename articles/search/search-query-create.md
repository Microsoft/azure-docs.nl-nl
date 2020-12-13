---
title: Een basisquery maken
titleSuffix: Azure Cognitive Search
description: Meer informatie over het maken van een query aanvraag in Cognitive Search, welke hulpprogram ma's en Api's moeten worden gebruikt voor testen en code en hoe query beslissingen beginnen met het ontwerp van de index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ace887396bacf264f0ffbd186ef1349e96496786
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371124"
---
# <a name="create-a-basic-query-in-azure-cognitive-search"></a>Een eenvoudige query maken in azure Cognitive Search

In dit artikel wordt stapsgewijs uitgelegd hoe u een query uitvoert. Voor beelden zijn in REST, zodat u teken reeksen kunt kopiëren naar **Search Explorer** in de portal of query's interactief bouwt met behulp van Postman of Visual Studio code. U kunt elke laag of versie van Cognitive Search gebruiken voor de voor beelden in dit artikel.

## <a name="choose-a-tool-or-api"></a>Kies een hulp programma of API

Kies uit de volgende hulpprogram ma's en Api's om query's te maken voor het testen of produceren van werk belastingen.

| Methodologie | Description |
|-------------|-------------|
| Portal| [Search Explorer (Portal)](search-explorer.md) bevat een zoek balk en opties voor selecties van index en API-versie. Resultaten worden geretourneerd als JSON-documenten. Wordt aanbevolen voor vroegtijdig onderzoek, testen en validatie. <br/>[Meer informatie.](search-explorer.md) |
| Hulpprogram ma's voor webtest| [Postman of Visual Studio code](search-get-started-rest.md) is een goede keuze voor het formuleren van [Zoek documenten](/rest/api/searchservice/search-documents) rest-aanroepen. De REST API ondersteunt elke programmatische bewerking in azure Cognitive Search, zodat u aanvragen interactief kunt verlenen om te begrijpen hoe het werkt voordat u in code investeert.  |
| Azure SDK | [SearchClient (.net)](/dotnet/api/azure.search.documents.searchclient) kan worden gebruikt voor het opvragen van een zoek index in C#.  [Meer informatie.](search-howto-dotnet-sdk.md) <br/><br/>[SearchClient (python)](/dotnet/api/azure.search.documents.searchclient) kan worden gebruikt om een zoek index in python op te vragen. [Meer informatie.](search-get-started-python.md) <br/><br/> [SearchClient (Java script)](/dotnet/api/azure.search.documents.searchclient) kan worden gebruikt voor het opvragen van een zoek index in Java script. [Meer informatie.](search-get-started-javascript.md)  |

## <a name="set-up-a-search-client"></a>Een Search-client instellen

Een zoek-client verifieert de zoek service, verzendt aanvragen en verwerkt reacties. Query's worden altijd door gestuurd bij de verzameling documenten van één index. U kunt geen indexen samen voegen of aangepaste of tijdelijke gegevens structuren maken als een query doel.

### <a name="in-the-portal"></a>In de portal

Search Explorer en andere portal-hulpprogram ma's hebben een ingebouwde client verbinding met de service, met directe toegangs indexen en andere objecten van portal-pagina's. Als u toegang hebt tot hulpprogram ma's, wizards en objecten, wordt ervan uitgegaan dat u over beheerders rechten voor de service beschikt. Met Search Explorer kunt u zich richten op het opgeven van de zoek reeks en andere para meters. 

### <a name="using-rest"></a>REST gebruiken

Voor REST-aanroepen kunt u [postman of soort gelijke hulp middelen](search-get-started-rest.md) gebruiken als de client om een aanvraag voor [Zoek documenten](/rest/api/searchservice/search-documents) op te geven. Elke aanvraag is zelfstandig, dus u moet het eind punt (URL naar de service) en een beheerder-of query-API-sleutel voor toegang opgeven. Afhankelijk van de aanvraag, kan de URL ook de index naam, de verzameling documenten en andere eigenschappen bevatten. Er worden een paar eigenschappen, zoals het inhouds type en de API-sleutel, door gegeven in de aanvraag header. Andere para meters kunnen worden door gegeven aan de URL of in de hoofd tekst van de aanvraag. Voor alle REST-aanroepen is een API-sleutel voor verificatie en een API-versie vereist.

### <a name="using-azure-sdks"></a>Azure Sdk's gebruiken

De Azure-Sdk's bieden Zoek clients die de status kunnen behouden, waardoor verbinding opnieuw kan worden gebruikt. Voor query bewerkingen maakt u een instantie van een SearchClient en geeft u waarden op voor de volgende eigenschappen: eind punt, sleutel, index. Vervolgens kunt u de Zoek methode aanroepen om de query teken reeks op te geven. 

| Taal | Client | Voorbeeld |
|----------|--------|---------|
| C# en .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Uw eerste Zoek query in C verzenden #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Uw eerste Zoek query in python verzenden](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Uw eerste Zoek query verzenden in Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| Javascript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Uw eerste Zoek query verzenden in Java script](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Kies een parser: eenvoudig | waard

Azure Cognitive Search biedt u een keuze tussen twee query-parsers voor het verwerken van normale en gespecialiseerde query's. Aanvragen die gebruikmaken van de eenvoudige parser zijn doorgaans Zoek opdrachten in de volledige tekst, geformuleerd met de [eenvoudige query syntaxis](query-simple-syntax.md), geselecteerd als de standaard waarde voor de snelheid en effectiviteit in vrije-tekst query's. Deze syntaxis ondersteunt een aantal veelgebruikte Zoek operatoren, waaronder de Opera tors en, of, niet, frase, achtervoegsel en voor rang.

De [volledige lucene-query syntaxis](query-Lucene-syntax.md#bkmk_syntax), ingeschakeld wanneer u `queryType=full` aan de aanvraag toevoegt, geeft de uitgebreide en duidelijke query taal weer die is ontwikkeld als onderdeel van [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). De volledige syntaxis breidt de eenvoudige syntaxis uit. Alle query's die u voor de eenvoudige syntaxis schrijft, worden uitgevoerd onder de volledige lucene-parser. 

De volgende voor beelden illustreren het punt: dezelfde query, maar met verschillende **`queryType`** instellingen, waardoor verschillende resultaten worden verkregen. In de eerste query wordt de `^3` After `historic` beschouwd als onderdeel van de zoek term. Het hoogste geclassificeerde resultaat voor deze query is "Marquis Plaza & Suites", die *Oceaan* in de beschrijving heeft.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

Dezelfde query die gebruikmaakt van de volledige lucene-parser, interpreteert `^3` als een in-Field Booster. Switch parsers wijzigen de positie, met de resultaten met de term *historisch* naar boven.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="enable-query-behaviors-in-an-index"></a>Query gedrag in een index inschakelen

Het ontwerp van de index en het query ontwerp zijn nauw verbonden met Azure Cognitive Search. Het *index schema*, met kenmerken voor elk veld, bepaalt het type query dat u kunt maken.

Index kenmerken voor een veld stel de toegestane bewerkingen in: Hiermee stelt u in dat een veld in de index kan worden *doorzocht* en kan worden *opgehaald* , *gesorteerd*, *gefilterd*, enzovoort. In de voorbeeld query's `"$orderby": "Rating desc"` werkt alleen, omdat het veld classificatie is gemarkeerd als *sorteerbaar* in het index schema.

![Index definitie voor het voor beeld van een hotel](./media/search-query-overview/hotel-sample-index-definition.png "Index definitie voor het voor beeld van een hotel")

De bovenstaande scherm afbeelding is een gedeeltelijke lijst met index kenmerken voor de voor [beeld-index](search-get-started-portal.md)van de hotels. U kunt het volledige index schema maken en weer geven in de portal. Zie [Create Index (rest API) (Engelstalig)](/rest/api/searchservice/create-index)voor meer informatie over index kenmerken.

## <a name="next-steps"></a>Volgende stappen

Nu u begrijpt hoe de aanvraag is gemaakt, probeert u voor beelden om de eenvoudige en volledige syntaxis te gebruiken.

+ [Voorbeelden van eenvoudige query's](search-query-simple-examples.md)
+ [Voor beelden van Lucene-syntaxis query's voor het maken van geavanceerde query's](search-query-lucene-examples.md)
+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)