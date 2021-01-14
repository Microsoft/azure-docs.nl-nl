---
title: Werken met zoek resultaten
titleSuffix: Azure Cognitive Search
description: Structuur en sorteer de zoek resultaten, haal een document telling op en voeg inhouds navigatie toe aan Zoek resultaten in azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: a7171d656ec9f839aea4ae73763ec6ebd20c2bb3
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209828"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Werken met zoek resultaten in azure Cognitive Search

In dit artikel wordt uitgelegd hoe u een query-antwoord kunt formuleren in azure Cognitive Search. De structuur van een antwoord wordt bepaald door para meters in de query: [document zoeken](/rest/api/searchservice/Search-Documents) in de rest API-of [searchresults-klasse](/dotnet/api/azure.search.documents.models.searchresults-1) in de .NET SDK. De para meters in de query kunnen worden gebruikt om de resultatenset op de volgende manieren te structureren:

+ Het aantal documenten in de resultaten beperken of batcheren (standaard 50)
+ Velden selecteren die in de resultaten moeten worden meegenomen
+ Order resultaten
+ Een overeenkomende hele of gedeeltelijke term markeren in de hoofd tekst van de zoek resultaten

## <a name="result-composition"></a>Resultaten samen stelling

Hoewel een zoek document kan bestaan uit een groot aantal velden, zijn er meestal slechts enkele van de waarden nodig om elk document in de resultatenset aan te duiden. Voeg op een query aanvraag `$select=<field list>` toe om op te geven welke velden in het antwoord worden weer gegeven. Een veld moet worden toegeschreven als kan worden **opgehaald** in de index om in een resultaat te worden opgenomen. 

Voor velden die het beste werken zijn dit het contrast en het onderscheid tussen documenten, waardoor er voldoende informatie is om een klik-en-antwoord op het deel van de gebruiker uit te nodigen. Op een e-commerce-site kunnen het een product naam, beschrijving, merk, kleur, grootte, prijs en classificatie zijn. Voor de hotels-voor beeld-index ingebouwde steek proef zijn dit mogelijk velden in het volgende voor beeld:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Als u afbeeldings bestanden wilt toevoegen aan een resultaat, zoals een foto van een product of logo, slaat u deze buiten Azure Cognitive Search op, maar voegt u een veld in uw index toe om te verwijzen naar de afbeeldings-URL in het zoek document. Voor beelden van indexen die afbeeldingen in de resultaten ondersteunen, zijn de **realestate-voor beeld-US-** demo, in deze [Snelstartgids](search-create-app-portal.md)en in de [nieuwe demo-app voor taken in Utrecht City](https://aka.ms/azjobsdemo).

### <a name="tips-for-unexpected-results"></a>Tips voor onverwachte resultaten

Af en toe zijn de stoffen en niet de structuur van de resultaten onverwacht. Wanneer de query resultaten onverwacht zijn, kunt u deze query wijzigingen proberen om te zien of het gaat om het volgende:

+ Wijzig **`searchMode=any`** (standaard) in **`searchMode=all`** om overeenkomsten te vereisen voor alle criteria in plaats van een criterium. Dit geldt met name wanneer Boole-Opera tors worden opgenomen in de query.

+ Experimenteer met verschillende lexicale analyse functies of aangepaste analyse functies om te zien of het resultaat van de query wordt gewijzigd. Met de standaard-Analyzer worden woord afgebroken woorden gesplitst en worden woorden naar hoofd formulieren verminderd, wat doorgaans de robuustheid van een query-antwoord verbetert. Als u echter afbreek streepjes wilt behouden of als teken reeksen speciale tekens bevatten, moet u mogelijk aangepaste analyse functies configureren om ervoor te zorgen dat de index tokens in de juiste indeling bevat. Zie voor meer informatie zoeken op de [gedeeltelijke term en patronen met speciale tekens (afbreek streepjes, joker tekens, regex, patronen)](search-query-partial-matching.md).

## <a name="paging-results"></a>Resultaten pagineren

Standaard retourneert de zoek machine tot de eerste 50 overeenkomsten, zoals wordt bepaald door de zoek score als de query volledige tekst zoekt, of in een wille keurige volg orde voor exacte overeenkomst query's.

Om een ander aantal overeenkomende documenten te retour neren, `$top` voegt `$skip` u para meters toe aan de query aanvraag. In de volgende lijst wordt de logica uitgelegd.

+ Voeg toe `$count=true` om een telling van het totaal aantal overeenkomende documenten in een index op te halen.

+ De eerste set van 15 overeenkomende documenten plus een telling van het totaal aantal overeenkomsten retour neren: `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Retourneert de tweede set, waarbij de eerste 15 wordt overgeslagen om de volgende 15 te verkrijgen: `$top=15&$skip=15` . Doe hetzelfde voor de derde set van 15: `$top=15&$skip=30`

De resultaten van gepagineerde query's zijn niet gegarandeerd stabiel als de onderliggende index wordt gewijzigd. Paginering wijzigt de waarde van `$skip` voor elke pagina, maar elke query is onafhankelijk en werkt op de huidige weer gave van de gegevens in de index op het moment van de query (met andere woorden, er is geen caching of moment opname van de resultaten, zoals de waarden in een Data Base voor algemeen gebruik).
 
Hieronder ziet u een voor beeld van hoe u dubbele waarden kunt krijgen. Aannemen een index met vier documenten:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
Stel nu dat er resultaten twee per keer worden geretourneerd, gesorteerd op waardering. U voert deze query uit om de eerste pagina met resultaten op te halen: `$top=2&$skip=0&$orderby=rating desc` de volgende resultaten worden geproduceerd:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
In de service wordt ervan uitgegaan dat er een vijfde document wordt toegevoegd aan de index in tussen query-aanroepen: `{ "id": "5", "rating": 4 }` .  Vervolgens voert u een query uit om de tweede pagina op te halen: `$top=2&$skip=2&$orderby=rating desc` en haalt u de volgende resultaten op:

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
U ziet dat document 2 twee keer wordt opgehaald. De reden hiervoor is dat het nieuwe document 5 een hogere waarde voor classificatie heeft, zodat het wordt gesorteerd vóór document 2 en-land op de eerste pagina. Hoewel dit gedrag mogelijk onverwacht is, is het gebruikelijk dat een zoek machine zich gedraagt.

## <a name="ordering-results"></a>Resultaten ordenen

Voor Zoek opdrachten in volledige tekst worden de resultaten automatisch gerangschikt op basis van een zoek Score, berekend aan de hand van term frequentie en nabijheid in een document (afgeleid van [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)), met hogere scores naar documenten met meer of betere overeenkomsten voor een zoek term. 

Zoek scores geven algemeen inzicht in de relevantie aan, die overeenkomen met de sterkte van de overeenkomst ten opzichte van andere documenten in dezelfde resultatenset. Scores zijn echter niet altijd consistent met de ene query naar de volgende, dus wanneer u met query's werkt, kunnen er kleine verschillen optreden in de manier waarop Zoek documenten worden besteld. Er zijn verschillende uitleg waarom dit kan gebeuren.

| Oorzaak | Beschrijving |
|-----------|-------------|
| Gegevens volatiliteit | De inhoud van de index is afhankelijk van het toevoegen, wijzigen of verwijderen van documenten. De term frequenties worden gewijzigd wanneer index updates na verloop van tijd worden verwerkt, waardoor de zoek scores van overeenkomende documenten worden beïnvloed. |
| Meerdere replica's | Voor services die gebruikmaken van meerdere replica's, worden query's op elke replica parallel verleend. De index statistieken die worden gebruikt voor het berekenen van een zoek Score worden berekend per replica, met de resultaten samengevoegd en geordend in het query-antwoord. Replica's zijn voornamelijk Spie gels van elkaar, maar statistieken kunnen verschillen als gevolg van kleine verschillen in de status. Eén replica kan bijvoorbeeld verwijderde documenten hebben die bijdragen aan hun statistieken, die uit andere replica's zijn samengevoegd. Normaal gesp roken zijn verschillen in statistieken per replica duidelijker in kleinere indexen. |
| Identieke scores | Als meerdere documenten dezelfde score hebben, kan het zijn dat deze eerst worden weer gegeven.  |

### <a name="how-to-get-consistent-ordering"></a>Consistente volg orde krijgen

Als consistente volg orde een toepassings vereiste is, kunt u expliciet een [ **`$orderby`** expressie](query-odata-filter-orderby-syntax.md) definiëren voor een veld. Alleen velden die zijn geïndexeerd als **`sortable`** kunnen worden gebruikt om resultaten te best Ellen. Velden die meestal worden gebruikt in de **`$orderby`** velden classificatie, datum en locatie als u de waarde van de **`orderby`** para meter opgeeft om veld namen op te vragen en de [**`geo.distance()` functie**](query-odata-filter-orderby-syntax.md) voor georuimtelijke waarden te gebruiken.

Een andere benadering die consistentie bevordert, is het gebruik van een [aangepast Score profiel](index-add-scoring-profiles.md). Score profielen bieden u meer controle over de rang schikking van items in de zoek resultaten, met de mogelijkheid om overeenkomsten te verhogen die in specifieke velden worden gevonden. De extra waarderings logica kan u helpen kleine verschillen tussen replica's te overschrijven omdat de zoek scores voor elk document verder uit elkaar liggen. U wordt aangeraden het [classificatie algoritme](index-ranking-similarity.md) voor deze benadering te hanteren.

## <a name="hit-highlighting"></a>Markeren

Treffer markeringen verwijst naar tekst opmaak (zoals vette of gele hooglichten) die wordt toegepast op overeenkomende termen in een resultaat, zodat u de overeenkomst eenvoudig kunt herkennen. Instructies voor het markeren van treffers worden vermeld in de [query-aanvraag](/rest/api/searchservice/search-documents). 

Als u het markeren van treffers wilt inschakelen, voegt `highlight=[comma-delimited list of string fields]` u in om op te geven welke velden markeringen zullen gebruiken. Markeren is handig voor meer inhouds velden, zoals een beschrijvings veld, waarbij de overeenkomst niet onmiddellijk duidelijk is. Alleen veld definities die als **doorzoekbaar** zijn gemarkeerd, komen in aanmerking voor treffer markeringen.

Azure Cognitive Search retourneert standaard Maxi maal vijf hooglichten per veld. U kunt dit nummer aanpassen door toe te voegen aan het veld een streepje gevolgd door een geheel getal. `highlight=Description-10`Retourneert bijvoorbeeld Maxi maal 10 hooglichten voor overeenkomende inhoud in het veld Beschrijving.

De opmaak wordt toegepast op volledige term query's. Het type opmaak wordt bepaald door Tags en en `highlightPreTag` `highlightPostTag` uw code verwerkt het antwoord (bijvoorbeeld het Toep assen van een vet letter type of een gele achtergrond).

In het volgende voor beeld zijn de termen "zand", "zand", "stranden", "strand" in het veld Beschrijving gemarkeerd voor markeren. Query's die query-uitbrei ding activeren in de-engine, zoals fuzzy en zoeken met Joker tekens, hebben beperkte ondersteuning voor het markeren van treffers.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Nieuw gedrag (vanaf 15 juli)

Services die zijn gemaakt na 15 juli 2020 bieden een andere markerings ervaring. Services die vóór deze datum zijn gemaakt, worden niet gewijzigd in hun markerings gedrag. 

Met het nieuwe gedrag:

* Alleen zinsdelen die overeenkomen met de volledige woordgroepen query worden geretourneerd. De query "Super Bowl" retourneert hooglichten als volgt:

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  Houd er rekening mee dat de term *Bowlen van chips* geen markeringen heeft omdat deze niet overeenkomt met de volledige woord groep.

Wanneer u client code schrijft waarmee treffer markeringen worden geïmplementeerd, moet u rekening houden met deze wijziging. Houd er rekening mee dat dit geen invloed heeft op u tenzij u een volledig nieuwe zoek service maakt.

## <a name="next-steps"></a>Volgende stappen

Als u snel een zoek pagina voor uw client wilt genereren, kunt u de volgende opties overwegen:

+ Met [toepassings Generator](search-create-app-portal.md)maakt u in de portal een HTML-pagina met een zoek balk, facet navigatie en resultaten gebied met afbeeldingen.
+ [Uw eerste app maken in C#](tutorial-csharp-create-first-app.md) is een zelf studie die een functionele client bouwt. Voorbeeld code toont gepagineerde query's, markeren van treffers en sorteren.

Enkele voor beelden van code zijn een web-front-end-interface, die u hier kunt vinden: [New York City Jobs demo app](https://aka.ms/azjobsdemo), [Java script-voorbeeld code met een live demo site](https://github.com/liamca/azure-search-javascript-samples)en [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).
