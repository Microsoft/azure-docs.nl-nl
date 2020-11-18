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
ms.openlocfilehash: ae4dd8b82e40b46da52a1b1f396569fda1dfea2b
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694623"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Gebruik de ' volledige ' lucene-Zoek syntaxis (geavanceerde query's in azure Cognitive Search)

Wanneer u query's voor Azure Cognitive Search bouwt, kunt u de standaard [eenvoudige query-parser](query-simple-syntax.md) vervangen door de meer expansieve, [lucene-query-parser in azure Cognitive Search](query-lucene-syntax.md) om gespecialiseerde en geavanceerde query definities te formuleren. 

De Lucene-parser ondersteunt complexe query constructies, zoals query's met een veld bereik, zoek acties op zoek opdrachten, infix-en achtervoegsel joker tekens zoeken, proximity Search, term Boosting en reguliere expressie zoeken. De extra stroom wordt geleverd met extra verwerkings vereisten, zodat u een iets langere uitvoerings tijd verwacht. In dit artikel kunt u een voor beeld bekijken van het demonstreren van query bewerkingen die beschikbaar zijn wanneer u de volledige syntaxis gebruikt.

> [!Note]
> Veel van de gespecialiseerde query constructies die zijn ingeschakeld via de volledige lucene-query syntaxis, worden niet in [tekst geanalyseerd](search-lucene-query-architecture.md#stage-2-lexical-analysis), wat kan worden verrassende als u op basis van stam of lemmatisering verwacht. Lexicale analyse wordt alleen uitgevoerd op de volledige voor waarden (een term query of een woordgroepen query). Query typen met onvolledige voor waarden (voor voegsel query, Joker teken query, regex-query, fuzzy query) worden direct toegevoegd aan de query structuur, waarbij de analyse fase wordt omzeild. De enige trans formatie die wordt uitgevoerd op gedeeltelijke query termen is lowercasing. 
>

## <a name="formulate-requests-in-postman"></a>Aanvragen formuleren in postman

De volgende voor beelden maken gebruik van een NYC-zoek index voor taken die bestaat uit taken die beschikbaar zijn op basis van een gegevensset die wordt verschaft door de [stad New York open data](https://opendata.cityofnewyork.us/) Initiative. Deze gegevens mogen niet als actueel of volledig worden beschouwd. De index bevindt zich op een sandbox-service van micro soft. Dit betekent dat u geen Azure-abonnement of Azure Cognitive Search nodig hebt om deze query's uit te proberen.

Wat u nodig hebt, is postman of een gelijkwaardig hulp programma voor het uitgeven van een HTTP-aanvraag op GET. Zie [verkennen met rest-clients](search-get-started-rest.md)voor meer informatie.

### <a name="set-the-request-header"></a>De aanvraag header instellen

1. Stel in de aanvraag header het **inhouds type** in op `application/json` .

2. Voeg een **API-sleutel** toe en stel deze in op deze teken reeks: `252044BE3886FE4A8E3BAA4F595114BB` . Dit is een query sleutel voor de sandbox-zoek service die als host fungeert voor de index van de NYC-taken.

Nadat u de aanvraag header hebt opgegeven, kunt u deze opnieuw gebruiken voor alle query's in dit artikel, zodat alleen de teken reeks **Search =** wordt uitgewisseld. 

  :::image type="content" source="media/search-query-lucene-examples/postman-header.png" alt-text="Para meters ingesteld op de aanvraag header van postman" border="false":::

### <a name="set-the-request-url"></a>De aanvraag-URL instellen

Request is een GET-opdracht die wordt gekoppeld aan een URL met het Azure Cognitive Search-eind punt en de zoek teken reeks.

  :::image type="content" source="media/search-query-lucene-examples/postman-basic-url-request-elements.png" alt-text="OPHALEN van header van Postman-aanvraag" border="false":::

URL-samen stelling heeft de volgende elementen:

+ **`https://azs-playground.search.windows.net/`** is een zoek service in de sandbox die wordt onderhouden door het team van Azure Cognitive Search Development. 
+ **`indexes/nycjobs/`** is de index van de NYC-taken in de verzameling indexen van die service. Zowel de service naam als de index zijn vereist voor de aanvraag.
+ **`docs`** is de verzameling documenten die alle Doorzoek bare inhoud bevat. De query-API-sleutel die in de aanvraag header is gegeven, werkt alleen bij Lees bewerkingen die zijn gericht op de verzameling documenten.
+ **`api-version=2020-06-30`** Hiermee stelt u de API-versie in, een vereiste para meter voor elke aanvraag.
+ **`search=*`** is de query teken reeks, die in de eerste query null is, de eerste 50 resultaten retourneert (standaard).

## <a name="send-your-first-query"></a>Uw eerste query verzenden

Plak, als een verificatie stap, de volgende aanvraag in GET en klik op **verzenden**. Resultaten worden geretourneerd als uitgebreide JSON-documenten. Er worden volledige documenten geretourneerd, zodat u alle velden en alle waarden kunt weer geven.

Plak deze URL in een REST-client als een validatie stap en om de document structuur weer te geven.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*
  ```

De query teken reeks, **`search=*`** , is een niet-opgegeven zoek opdracht die gelijk is aan Null of een lege zoek opdracht. Het is de eenvoudigste zoek actie die u kunt uitvoeren.

U kunt eventueel ook toevoegen **`$count=true`** aan de URL om een telling van de documenten te retour neren die overeenkomen met de zoek criteria. In een lege Zoek reeks zijn dit alle documenten in de index (ongeveer 2800 in het geval van NYC-taken).

## <a name="how-to-invoke-full-lucene-parsing"></a>Volledige lucene-parsering aanroepen

Voeg **query type = Full** toe om de volledige query syntaxis op te roepen, waarbij de standaard syntaxis voor eenvoudige query's wordt genegeerd. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&search=*
```

Alle voor beelden in dit artikel geven de **query type = volledige** Zoek parameter op, waarmee wordt aangegeven dat de volledige syntaxis wordt verwerkt door de Lucene query-parser. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Voor beeld 1: query bereik op een lijst met velden

Dit eerste voor beeld is niet specifiek voor lucene, maar we leiden ernaar om het eerste fundamentele query concept te introduceren: veld bereik. In dit voor beeld wordt de volledige query en het antwoord op slechts enkele specifieke velden bereik. Het is belang rijk dat u weet hoe u een lees bare JSON-respons structureert wanneer uw hulp programma postman of Search Explorer is. 

Voor de boog is de query alleen gericht op het *business_title* veld en geeft u op dat er alleen zakelijke titels worden geretourneerd. Met de para meter **searchFields** wordt de uitvoering van query's beperkt tot alleen het business_title veld en **selecteert** u de velden die in het antwoord zijn opgenomen.

### <a name="search-expression"></a>Zoek expressie

```http
&search=*&searchFields=business_title&$select=business_title
```

Dit is dezelfde query met meerdere velden in een door komma's gescheiden lijst.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

De spaties na de komma's zijn optioneel.

> [!Tip]
> Wanneer u de REST API uit uw toepassings code gebruikt, vergeet dan niet om URL-versleutelings parameters zoals `$select` en `searchFields` .

### <a name="full-url"></a>Volledige URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Antwoord voor deze query moet er ongeveer uitzien als in de volgende scherm afbeelding.

  ![Postman-voorbeeld antwoord met scores](media/search-query-lucene-examples/postman-sample-results.png)

Mogelijk hebt u de zoek Score in het antwoord gezien. Een uniforme Score van 1 treedt op als er geen positie is, omdat de zoek opdracht niet in volledige tekst is gezocht, of omdat er geen criteria zijn toegepast. Voor Null-Zoek opdrachten zonder criteria worden rijen in een wille keurige volg orde weer gegeven. Wanneer u werkelijke zoek criteria opneemt, ziet u dat zoek scores worden weer geven in betekenis volle waarden.

## <a name="example-2-fielded-search"></a>Voor beeld 2: zoeken in een veld

De volledige lucene-syntaxis ondersteunt het bereik van individuele Zoek expressies naar een bepaald veld. In dit voor beeld wordt gezocht naar zakelijke titels met de term Senior, maar niet via Junior.

### <a name="search-expression"></a>Zoek expressie

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Dit is dezelfde query met meerdere velden.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Volledige URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Zoek expressie voor voorbeeld reacties van postman" border="false":::

U kunt een zoek bewerking in een veld definiëren met de syntaxis **FieldName: searchExpression** , waarbij de zoek expressie één woord of een woord groep of een complexere expressie tussen haakjes kan zijn, optioneel met Booleaanse Opera tors. Enkele voor beelden zijn:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Zorg ervoor dat u meerdere teken reeksen tussen aanhalings tekens plaatst als u wilt dat beide teken reeksen als één entiteit worden geëvalueerd. in dit geval zoekt u naar twee afzonderlijke locaties in het `state` veld. Zorg er ook voor dat de operator is gekapitaliseerd, omdat u niet en en kunt zien.

Het veld dat is opgegeven in **veld Naam: searchExpression** moet een doorzoekbaar veld zijn. Zie [Create Index (Azure Cognitive Search rest API)](/rest/api/searchservice/create-index) voor meer informatie over het gebruik van index kenmerken in veld definities.

> [!NOTE]
> In het bovenstaande voor beeld is het niet nodig om de `searchFields` para meter te gebruiken omdat elk deel van de query expliciet een veld naam heeft opgegeven. U kunt echter nog steeds de `searchFields` para meter gebruiken als u een query wilt uitvoeren waarbij sommige onderdelen naar een specifiek veld zijn beperkt, en de rest kan van toepassing zijn op verschillende velden. De query `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` zou bijvoorbeeld alleen overeenkomen met `senior NOT junior` het `business_title` veld, terwijl deze ' Extern ' zou overeenkomen met het `posting_type` veld. De naam van het veld in **FieldName: searchExpression** heeft altijd voor rang op de `searchFields` para meter, in dit voor beeld is het niet nodig om `business_title` in de para meter op te nemen `searchFields` .

## <a name="example-3-fuzzy-search"></a>Voor beeld 3: fuzzy Search

De volledige lucene-syntaxis biedt ook ondersteuning voor fuzzy Search, overeenkomend met termen met een vergelijk bare constructie. Als u een fuzzy zoek opdracht wilt uitvoeren, voegt u het tilde- `~` symbool toe aan het einde van één woord met een optionele para meter, een waarde tussen 0 en 2, waarmee de bewerkings afstand wordt opgegeven. U kunt bijvoorbeeld `blue~` een `blue~1` blauwe, blauwe en lijm retour neren.

### <a name="search-expression"></a>Zoek expressie

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Zinsdelen worden niet direct ondersteund, maar u kunt een overeenkomst opgeven die overeenkomt met de onderdelen van een woord groep.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Volledige URL

Met deze query zoekt u naar taken met de term ' koppelen ' (opzettelijk verkeerd gespeld):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Zoek antwoord bij benadering](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Fuzzy query's worden niet [geanalyseerd](search-lucene-query-architecture.md#stage-2-lexical-analysis). Query typen met onvolledige voor waarden (voor voegsel query, Joker teken query, regex-query, fuzzy query) worden direct toegevoegd aan de query structuur, waarbij de analyse fase wordt omzeild. De enige trans formatie die wordt uitgevoerd bij onvolledige query termen is lowercasing.
>

## <a name="example-4-proximity-search"></a>Voor beeld 4: proximity Search
Proximity-Zoek opdrachten worden gebruikt om termen te vinden die zich in een document nabij elkaar bevinden. Voeg een tilde ' ~ ' aan het einde van een woord groep toe, gevolgd door het aantal woorden dat de grens van de nabijheid heeft gemaakt. Als u bijvoorbeeld ' Hotel lucht haven ' ~ 5 krijgt, vindt u de termen Hotel en lucht haven binnen vijf woorden van elkaar in een document.

### <a name="search-expression"></a>Zoek expressie

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Volledige URL

In deze query, voor taken met de term ' senior analist ', waarbij deze wordt gescheiden door niet meer dan één woord:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Proximity-query" border="false":::

Probeer het opnieuw met het verwijderen van de woorden tussen de term ' Senior Analyst '. U ziet dat er 8 documenten voor deze query worden geretourneerd, in tegens telling tot 10 voor de vorige query.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Voor beeld 5: term versterking
De term Boosting verwijst naar een hoger niveau voor een document als dit de gestimuleerde term bevat, ten opzichte van documenten die de term niet bevatten. Als u een periode wilt verhogen, gebruikt u het caret-teken (^) met een boost factor (een getal) aan het einde van de term die u zoekt. 

### <a name="full-urls"></a>Volledige Url's

In deze ' before '-query kunt u zoeken naar taken met de term *computer analist* en ziet u dat er geen resultaten zijn met zowel de *computer* als de *analist* van de computer, maar *computers* zijn aan de bovenkant van de resultaten.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  :::image type="content" source="media/search-query-lucene-examples/termboostingbefore.png" alt-text="Duur verhogen vóór" border="false":::

In de query ' na ' herhaalt u de zoek opdracht. deze tijd verhoogt de resultaten met de term *analist* over de term *computer* als beide woorden niet bestaan. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Een meer menselijke Lees bare versie van de bovenstaande query is `search=business_title:computer analyst^2` . Voor een bewaarde query `^2` wordt gecodeerd als `%5E2` , wat moeilijker te zien is.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Duur verhogen na" border="false":::

De term Boosting verschilt van Score profielen in die score profielen om bepaalde velden te stimuleren, in plaats van specifieke voor waarden. In het volgende voor beeld worden de verschillen toegelicht.

Houd rekening met een score profiel dat overeenkomt met een bepaald veld, zoals een **genre** in het musicstoreindex-voor beeld. De term versterking kan worden gebruikt om bepaalde zoek termen hoger te verhogen dan andere. Bijvoorbeeld: ' Rock 3 2 Electronic ' verhoogt documenten met de zoek termen in het veld **genre** hoger dan andere Doorzoek bare velden in de index. Documenten die de zoek term ' Rock ' bevatten, krijgen bovendien hogere prioriteit dan de andere zoek term ' elektronisch ' als resultaat van de waarde voor de verhoging van de term (2).

Wanneer u het factor niveau instelt, des te hoger de Boost-factor, des te meer relevant de term is relatief ten opzichte van andere zoek termen. Standaard is de Boost factor 1. Hoewel de Boost-factor positief moet zijn, kan deze kleiner zijn dan 1 (bijvoorbeeld 0,2).


## <a name="example-6-regex"></a>Voor beeld 6: regex

Een reguliere expressie zoekt zoekt naar een overeenkomst op basis van de inhoud tussen slashes '/', zoals beschreven in de [klasse RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="search-expression"></a>Zoek expressie

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Volledige URL

In deze query zoekt u naar taken met de term senior of Junior: `search=business_title:/(Sen|Jun)ior/` .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Regex-query" border="false":::

> [!Note]
> Regex-query's worden niet [geanalyseerd](./search-lucene-query-architecture.md#stage-2-lexical-analysis). De enige trans formatie die wordt uitgevoerd bij onvolledige query termen is lowercasing.
>

## <a name="example-7-wildcard-search"></a>Voor beeld 7: zoeken met Joker tekens
U kunt de algemeen herkende syntaxis gebruiken voor Zoek opdrachten met Joker tekens met meerdere ( \* ) of één (?). Opmerking de Lucene-query-parser ondersteunt het gebruik van deze symbolen met één term en geen woord groep.

### <a name="search-expression"></a>Zoek expressie

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Volledige URL

In deze query zoekt u naar taken die het voor voegsel ' PROG ' bevatten. Dit zijn onder andere zakelijke titels met de termen Program meren en programmeurs. U kunt geen * of gebruiken. symbool als het eerste teken van een zoek opdracht.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Joker teken query" border="false":::

> [!Note]
> Query's met Joker tekens worden niet [geanalyseerd](./search-lucene-query-architecture.md#stage-2-lexical-analysis). De enige trans formatie die wordt uitgevoerd bij onvolledige query termen is lowercasing.
>

## <a name="next-steps"></a>Volgende stappen
Geef de Lucene-query-parser op in uw code. In de volgende koppelingen wordt uitgelegd hoe u zoek query's instelt voor zowel .NET als de REST API. De koppelingen gebruiken de standaard eenvoudige syntaxis, zodat u moet Toep assen wat u in dit artikel hebt geleerd om het **query type** op te geven.

* [Query's uitvoeren op uw index met behulp van de .NET SDK](./search-get-started-dotnet.md)
* [Query's uitvoeren op uw index met behulp van de REST API](./search-get-started-powershell.md)

Aanvullende Naslag informatie over syntaxis, query architectuur en voor beelden vindt u in de volgende koppelingen:

+ [Eenvoudige syntaxis query-voor beelden](search-query-simple-examples.md)
+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Eenvoudige query syntaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Volledige lucene-query syntaxis](/rest/api/searchservice/lucene-query-syntax-in-azure-search)