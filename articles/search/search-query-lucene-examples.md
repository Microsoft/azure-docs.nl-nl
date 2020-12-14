---
title: Volledige lucene-query syntaxis gebruiken
titleSuffix: Azure Cognitive Search
description: De Lucene-query syntaxis voor fuzzy zoeken, proximity Search, term boosting, reguliere expressies zoeken en zoek opdrachten met Joker tekens in een Azure Cognitive Search-service.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: df26cfc3b220f40a7e73ff1c750d2b2ae37e7625
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401454"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Gebruik de ' volledige ' lucene-Zoek syntaxis (geavanceerde query's in azure Cognitive Search)

Wanneer u query's voor Azure Cognitive Search bouwt, kunt u de standaard [eenvoudige query-parser](query-simple-syntax.md) vervangen door de krachtige [lucene-query-parser in azure Cognitive Search](query-lucene-syntax.md) om gespecialiseerde en geavanceerde query definities te formuleren. 

De Lucene-parser ondersteunt complexe query constructies, zoals query's met een veld bereik, zoek acties op zoek opdrachten, infix-en achtervoegsel joker tekens zoeken, proximity Search, term Boosting en reguliere expressie zoeken. De extra stroom wordt geleverd met extra verwerkings vereisten, zodat u een iets langere uitvoerings tijd verwacht. In dit artikel kunt u een voor beeld bekijken van het demonstreren van query bewerkingen op basis van de volledige syntaxis.

> [!Note]
> Veel van de gespecialiseerde query constructies die zijn ingeschakeld via de volledige lucene-query syntaxis, worden niet in [tekst geanalyseerd](search-lucene-query-architecture.md#stage-2-lexical-analysis), wat kan worden verrassende als u op basis van stam of lemmatisering verwacht. Lexicale analyse wordt alleen uitgevoerd op de volledige voor waarden (een term query of een woordgroepen query). Query typen met onvolledige voor waarden (voor voegsel query, Joker teken query, regex-query, fuzzy query) worden direct toegevoegd aan de query structuur, waarbij de analyse fase wordt omzeild. De enige trans formatie die wordt uitgevoerd op gedeeltelijke query termen is lowercasing. 
>

## <a name="nyc-jobs-examples"></a>Voor beelden van NYC-taken

De volgende voor beelden maken gebruik van de [zoek index](https://azjobsdemo.azurewebsites.net/)  van de NYC-taken die bestaat uit taken die beschikbaar zijn op basis van een gegevensset die wordt verschaft door de [stad van New York open data Initiative](https://nycopendata.socrata.com/). Deze gegevens mogen niet als actueel of volledig worden beschouwd. De index bevindt zich op een sandbox-service van micro soft. Dit betekent dat u geen Azure-abonnement of Azure Cognitive Search nodig hebt om deze query's uit te proberen.

Wat u nodig hebt, is postman of een gelijkwaardig hulp programma voor het uitgeven van een HTTP-aanvraag op GET of POST. Als u niet bekend bent met deze hulpprogram ma's, raadpleegt u [Quick Start: Azure Cognitive Search verkennen rest API](search-get-started-rest.md).

## <a name="set-up-the-request"></a>De aanvraag instellen

1. Aanvraag headers moeten de volgende waarden hebben:

   | Sleutel | Waarde |
   |-----|-------|
   | Content-Type | `application/json`|
   | API-sleutel  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (dit is de daad werkelijke query-API-sleutel voor de sandbox-zoek service die als host fungeert voor de index van de NYC-taken) |

1. Stel de bewerking in op **`GET`** .

1. De URL instellen op **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + De documenten verzameling in de index bevat alle Doorzoek bare inhoud. Een query-API-sleutel die in de aanvraag header is opgenomen, werkt alleen voor lees bewerkingen die zijn gericht op de verzameling documenten.

   + **`$count=true`** retourneert een telling van de documenten die voldoen aan de zoek criteria. In een lege Zoek reeks zijn de aantallen alle documenten in de index (ongeveer 2558 in het geval van NYC-taken).

   + **`search=*`** is een niet-opgegeven query die gelijk is aan Null of een lege zoek opdracht. Het is niet erg nuttig, maar het is de eenvoudigste zoek opdracht die u kunt uitvoeren. alle velden die kunnen worden opgehaald, worden weer gegeven in de index, met alle waarden.

   + **`queryType=full`** Hiermee wordt de volledige lucene Analyzer aangeroepen.

1. Plak, als een verificatie stap, de volgende aanvraag in GET en klik op **verzenden**. Resultaten worden geretourneerd als uitgebreide JSON-documenten.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>Volledige lucene-parsering aanroepen

Voeg toe **`queryType=full`** om de volledige query syntaxis aan te roepen en de standaard eenvoudige query syntaxis te vervangen. In alle voor beelden in dit artikel wordt de **`queryType=full`** Zoek parameter opgegeven, waarmee wordt aangegeven dat de volledige syntaxis wordt verwerkt door de Lucene query-parser. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "full"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Voor beeld 1: query bereik op een lijst met velden

Dit eerste voor beeld is geen parser-specifiek, maar we leiden ernaar om het eerste fundamentele query concept te introduceren: containment. In dit voor beeld worden zowel de uitvoering van query's als de reactie op slechts enkele specifieke velden beperkt. Het is belang rijk dat u weet hoe u een lees bare JSON-respons structureert wanneer uw hulp programma postman of Search Explorer is. 

Deze query streeft alleen naar *business_title* in **`searchFields`** , waarbij via de **`select`** para meter hetzelfde veld in het antwoord wordt opgegeven.

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Antwoord voor deze query moet er ongeveer uitzien als in de volgende scherm afbeelding.

  ![Postman-voorbeeld antwoord met scores](media/search-query-lucene-examples/postman-sample-results.png)

Mogelijk hebt u de zoek Score in het antwoord gezien. Een uniforme Score van **1** treedt op als er geen positie is, omdat de zoek opdracht niet in volledige tekst is gezocht, of omdat er geen criteria zijn ingesteld. Voor een lege zoek opdracht worden rijen in een wille keurige volg orde weer gegeven. Wanneer u werkelijke criteria opneemt, ziet u dat zoek scores worden weer geven in betekenis volle waarden.

## <a name="example-2-fielded-search"></a>Voor beeld 2: zoeken in een veld

De volledige lucene-syntaxis ondersteunt het bereik van individuele Zoek expressies naar een bepaald veld. In dit voor beeld wordt gezocht naar zakelijke titels met de term Senior, maar niet via Junior. U kunt meerdere velden opgeven met en.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

Antwoord voor deze query moet er ongeveer uitzien als de volgende scherm afbeelding (posting_type wordt niet weer gegeven).

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Zoek expressie voor voorbeeld reacties van postman" border="false":::

De zoek expressie kan bestaan uit één woord of een woord groep, of een complexere expressie tussen haakjes, optioneel met Booleaanse Opera tors. Enkele voor beelden zijn:

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

Zorg ervoor dat u meerdere teken reeksen tussen aanhalings tekens plaatst als u wilt dat beide teken reeksen als één entiteit worden geëvalueerd. in dit geval zoekt u naar twee afzonderlijke locaties in het `state` veld. Afhankelijk van het hulp programma, moet u mogelijk `\` de aanhalings tekens escapen. 

Het veld dat is opgegeven in **veld Naam: searchExpression** moet een doorzoekbaar veld zijn. Zie [Create Index (Azure Cognitive Search rest API)](/rest/api/searchservice/create-index) voor meer informatie over het gebruik van index kenmerken in veld definities.

> [!NOTE]
> In het bovenstaande voor beeld **`searchFields`** wordt de para meter wegge laten, omdat elk deel van de query expliciet een veld naam heeft opgegeven. U kunt echter nog steeds gebruiken **`searchFields`** als de query meerdere onderdelen bevat (met behulp van en-instructies). De query `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` zou bijvoorbeeld alleen overeenkomen met `senior NOT junior` het `business_title` veld, terwijl deze ' Extern ' zou overeenkomen met het `posting_type` veld. De naam van het veld in `fieldName:searchExpression` heeft altijd voor rang op **`searchFields`** , wat in dit voor beeld het geval is `business_title` **`searchFields`** .

## <a name="example-3-fuzzy-search"></a>Voor beeld 3: fuzzy Search

De volledige lucene-syntaxis biedt ook ondersteuning voor fuzzy Search, overeenkomend met termen met een vergelijk bare constructie. Als u een fuzzy zoek opdracht wilt uitvoeren, voegt u het tilde- `~` symbool toe aan het einde van één woord met een optionele para meter, een waarde tussen 0 en 2, waarmee de bewerkings afstand wordt opgegeven. U kunt bijvoorbeeld `blue~` een `blue~1` blauwe, blauwe en lijm retour neren.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Zinsdelen worden niet direct ondersteund, maar u kunt een exacte overeenkomst opgeven voor elke term van een woord groep met meerdere delen, zoals `search=business_title:asosiate~ AND comm~` .  In de onderstaande scherm afbeelding bevat het antwoord een overeenkomst voor de *Community-koppeling*.

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="Zoek antwoord bij benadering" border="false":::

> [!Note]
> Fuzzy query's worden niet [geanalyseerd](search-lucene-query-architecture.md#stage-2-lexical-analysis). Query typen met onvolledige voor waarden (voor voegsel query, Joker teken query, regex-query, fuzzy query) worden direct toegevoegd aan de query structuur, waarbij de analyse fase wordt omzeild. De enige trans formatie die wordt uitgevoerd op gedeeltelijke query termen is lowercasing.
>

## <a name="example-4-proximity-search"></a>Voor beeld 4: proximity Search

Proximity-Zoek opdrachten worden gebruikt om termen te vinden die zich in een document nabij elkaar bevinden. Voeg een tilde ' ~ ' aan het einde van een woord groep toe, gevolgd door het aantal woorden dat de grens van de nabijheid heeft gemaakt. Als u bijvoorbeeld ' Hotel lucht haven ' ~ 5 krijgt, vindt u de termen Hotel en lucht haven binnen vijf woorden van elkaar in een document.

Met deze query wordt gezocht naar de termen "Senior" en "analist", waarbij elke term wordt gescheiden door niet meer dan één woord, en de aanhalings tekens worden met een escape teken ( `\"` ) om de woord groep te behouden:

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Antwoord voor deze query moet er ongeveer uitzien als de volgende scherm afbeelding 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Proximity-query" border="false":::

Probeer het opnieuw, waardoor de afstand ( `~0` ) tussen de termen ' Senior Analyst ' wordt vermeden. U ziet dat er 8 documenten voor deze query worden geretourneerd, in tegens telling tot 10 voor de vorige query.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>Voor beeld 5: term versterking

De term Boosting verwijst naar een hoger niveau voor een document als dit de gestimuleerde term bevat, ten opzichte van documenten die de term niet bevatten. Als u een periode wilt verhogen, gebruikt u het caret- `^` teken () met een boost factor (een getal) aan het einde van de term die u zoekt.

In deze ' before '-query kunt u zoeken naar taken met de term *computer analist* en ziet u dat er geen resultaten zijn met zowel de *computer* als de *analist* van de computer, maar *computers* zijn aan de bovenkant van de resultaten.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

In de query ' na ' herhaalt u de zoek opdracht. deze tijd verhoogt de resultaten met de term *analist* over de term *computer* als beide woorden niet bestaan. Een door menselijke Lees bare versie van de query is `search=business_title:computer analyst^2` . Voor een bewaarde query in postman `^2` wordt gecodeerd als `%5E2` .

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Antwoord voor deze query moet er ongeveer uitzien als in de volgende scherm afbeelding.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Duur verhogen na" border="false":::

De term Boosting verschilt van Score profielen in die score profielen om bepaalde velden te stimuleren, in plaats van specifieke voor waarden. In het volgende voor beeld worden de verschillen toegelicht.

Houd rekening met een score profiel dat overeenkomt met een bepaald veld, zoals een **genre** in het musicstoreindex-voor beeld. De term versterking kan worden gebruikt om bepaalde zoek termen hoger te verhogen dan andere. Bijvoorbeeld: ' Rock 3 2 Electronic ' verhoogt documenten met de zoek termen in het veld **genre** hoger dan andere Doorzoek bare velden in de index. Documenten die de zoek term ' Rock ' bevatten, krijgen bovendien hogere prioriteit dan de andere zoek term ' elektronisch ' als resultaat van de waarde voor de verhoging van de term (2).

Wanneer u het factor niveau instelt, des te hoger de Boost-factor, des te meer relevant de term is relatief ten opzichte van andere zoek termen. Standaard is de Boost factor 1. Hoewel de Boost-factor positief moet zijn, kan deze kleiner zijn dan 1 (bijvoorbeeld 0,2).

## <a name="example-6-regex"></a>Voor beeld 6: regex

Een reguliere expressie zoekt zoekt naar een overeenkomst op basis van de inhoud tussen slashes '/', zoals beschreven in de [klasse RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Antwoord voor deze query moet er ongeveer uitzien als in de volgende scherm afbeelding.

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Regex-query" border="false":::

> [!Note]
> Regex-query's worden niet [geanalyseerd](./search-lucene-query-architecture.md#stage-2-lexical-analysis). De enige trans formatie die wordt uitgevoerd op gedeeltelijke query termen is lowercasing.
>

## <a name="example-7-wildcard-search"></a>Voor beeld 7: zoeken met Joker tekens

U kunt de algemeen herkende syntaxis gebruiken voor Zoek opdrachten met Joker tekens met meerdere ( \* ) of één (?). Opmerking de Lucene-query-parser ondersteunt het gebruik van deze symbolen met één term en geen woord groep.

In deze query zoekt u naar taken die het voor voegsel ' PROG ' bevatten. Dit zijn onder andere zakelijke titels met de termen Program meren en programmeurs. U kunt een `*` symbool of niet gebruiken `?` als het eerste teken van een zoek opdracht.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Antwoord voor deze query moet er ongeveer uitzien als in de volgende scherm afbeelding.

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Joker teken query" border="false":::

> [!Note]
> Query's met Joker tekens worden niet [geanalyseerd](./search-lucene-query-architecture.md#stage-2-lexical-analysis). De enige trans formatie die wordt uitgevoerd op gedeeltelijke query termen is lowercasing.
>

## <a name="next-steps"></a>Volgende stappen

Probeer query's in code op te geven. In de volgende koppelingen wordt uitgelegd hoe u zoek query's kunt instellen met behulp van de Azure Sdk's.

+ [Query's uitvoeren op uw index met behulp van de .NET SDK](search-get-started-dotnet.md)
+ [Query's uitvoeren op uw index met behulp van de python-SDK](search-get-started-python.md)
+ [Query's uitvoeren op uw index met behulp van de Java script SDK](search-get-started-javascript.md)

Aanvullende Naslag informatie over syntaxis, query architectuur en voor beelden vindt u in de volgende koppelingen:

+ [Voor beelden van Lucene-syntaxis query's voor het maken van geavanceerde query's](search-query-lucene-examples.md)
+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Eenvoudige query syntaxis](query-simple-syntax.md)
+ [Volledige lucene-query syntaxis](query-lucene-syntax.md)
+ [Filter syntaxis](search-query-odata-filter.md)