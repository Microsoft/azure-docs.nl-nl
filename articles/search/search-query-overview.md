---
title: Typen query's
titleSuffix: Azure Cognitive Search
description: Meer informatie over de typen query's die worden ondersteund in Cognitive Search, zoals vrije tekst, filter, automatisch aanvullen en suggesties, geo-zoeken, systeem query's en zoeken naar documenten.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 97b0a4ca3e4fb94a21cbd30a27a3037f45fed782
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487114"
---
# <a name="querying-in-azure-cognitive-search"></a>Query's uitvoeren in azure Cognitive Search

Azure Cognitive Search biedt een uitgebreide query taal ter ondersteuning van een breed scala aan scenario's, van zoeken in vrije tekst tot zeer specifieke query patronen. In dit artikel worden query aanvragen beschreven, en wat voor soort query's u kunt maken.

In Cognitive Search is een query een volledige specificatie van een round-trip- **`search`** bewerking, met para meters waarmee de uitvoering van query's kan worden gewaarschuwd en het antwoord wordt weer gegeven. Para meters en parsers bepalen het type query aanvraag. Het volgende query voorbeeld is een vrije-tekst query met een Booleaanse operator, met behulp van de [Zoek documenten (rest API)](/rest/api/searchservice/search-documents), gericht op de verzameling [hotels-voor beeld-index](search-get-started-portal.md) documenten.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple",
    "searchMode": "all",
    "search": "restaurant +view",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

De para meters die worden gebruikt tijdens het uitvoeren van query's zijn:

+ **`queryType`** Hiermee stelt u de parser in, ofwel de [standaard-query-parser](search-query-simple-examples.md) (optimaal voor zoeken in volledige tekst), of de [volledige lucene-query-parser](search-query-lucene-examples.md) die wordt gebruikt voor geavanceerde query constructies, zoals reguliere expressies, proximity Search, fuzzy en Joker tekens zoeken.

+ **`searchMode`** Hiermee wordt aangegeven of overeenkomsten zijn gebaseerd op criteria voor ' alle ' of ' elk criterium ' in de expressie. De standaard waarde is any.

+ **`search`** biedt de match criteria, meestal hele termen of zinsdelen, met of zonder Opera tors. Elk veld waarvoor een *zoekbaar* kenmerk is in het index schema, is een kandidaat voor deze para meter.

+ **`searchFields`** beperkt de uitvoering van query's naar specifieke Doorzoek bare velden. Tijdens de ontwikkeling is het handig om dezelfde velden lijst te gebruiken voor selecteren en zoeken. Anders is het mogelijk dat een overeenkomst is gebaseerd op veld waarden die u niet in de resultaten kunt zien, waardoor er onzekerheid is over de reden waarom het document is geretourneerd.

Para meters die worden gebruikt om de reactie te vormen:

+ **`select`** geeft aan welke velden moeten worden geretourneerd in het antwoord. Alleen velden die zijn gemarkeerd als *ophalen* in de index kunnen worden gebruikt in een SELECT-instructie.

+ **`top`** retourneert het opgegeven aantal best overeenkomende documenten. In dit voor beeld worden slechts tien hits geretourneerd. Als u de resultaten wilt weer geven, kunt u met de bovenkant en de pagina overs Laan.

+ **`count`** geeft aan hoeveel documenten in de volledige index in zijn geheel overeenkomen, wat meer kan zijn dan wat er wordt geretourneerd. 

+ **`orderby`** wordt gebruikt als u resultaten wilt sorteren op een waarde, zoals een classificatie of locatie. Anders is de standaard waarde het gebruik van de relevantie score om de resultaten te classificeren. Een veld moet worden voorzien van het kenmerk *sorteerbaar* als kandidaat voor deze para meter.

De bovenstaande lijst is representatief maar niet limitatief. Zie [documenten zoeken (rest API)](/rest/api/searchservice/search-documents)voor een volledige lijst met para meters voor een query aanvraag.

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typen query's

Met enkele belang rijke uitzonde ringen wordt een query aanvraag herhaald in omgekeerde indexen die zijn gestructureerd voor snelle scans, waarbij een overeenkomst kan worden gevonden in mogelijk elk wille keurig veld in een wille keurig aantal Zoek documenten. In Cognitive Search is de primaire methodologie voor het vinden van overeenkomsten zoeken in volledige tekst of filters, maar u kunt ook andere bekende zoek functies implementeren, zoals automatisch aanvullen, of zoeken op geografische locatie. De rest van dit artikel bevat een overzicht van query's in Cognitive Search en bevat koppelingen naar meer informatie en voor beelden.

## <a name="full-text-search"></a>Zoeken in volledige tekst

Als uw zoek toepassing een zoekvak bevat waarmee term invoer wordt verzameld, is zoek opdracht in volledige tekst waarschijnlijk de query bewerking. Zoeken in volledige tekst accepteert voor waarden of zinsdelen die zijn door gegeven in een **`search`** para meter in alle *Doorzoek* bare velden in uw index. Optionele Booleaanse Opera tors in de query teken reeks kunnen insluitings-of uitsluitings criteria opgeven. De eenvoudige parser en volledige parser ondersteunen zoeken in volledige tekst.

In Cognitive Search is zoeken in volledige tekst gebaseerd op de Apache Lucene-query-engine. Query reeksen in zoeken in volledige tekst worden lexicale analyse om scans efficiënter te maken. Analyse omvat alle voor waarden voor alle termen, waarbij stop woorden zoals "de" en de voor waarden voor primitieve hoofd formulieren worden verminderd. De standaard-Analyzer is standaard-lucene.

Wanneer overeenkomende voor waarden worden gevonden, wordt in de query-engine een zoek document met de overeenkomst met de document sleutel of ID voor het verzamelen van veld waarden weer gegeven, worden de documenten gerangschikt op basis van relevantie en wordt de bovenste 50 (standaard) geretourneerd in het antwoord of een ander nummer als u hebt opgegeven **`top`** .

Als u Zoek opdrachten in volledige tekst implementeert, kunt u zien hoe u met de tokens van uw inhoud fouten oplost bij het opsporen van problemen met query's. Query's over teken reeksen met afbreek streepjes of speciale tekens kunnen nood zakelijk een andere analysator dan de standaard-lucene gebruiken om ervoor te zorgen dat de index de juiste tokens bevat. U kunt de standaard instelling vervangen door [taal analysen](index-add-language-analyzers.md#language-analyzer-list) of [gespecialiseerde](index-add-custom-analyzers.md#AnalyzerTable) analyse functies waarmee de lexicale analyse wordt gewijzigd. Een voor beeld is een [tref woord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) dat de volledige inhoud van een veld als één token behandelt. Dit is handig voor gegevens zoals post codes, Id's en sommige product namen. Zie voor meer informatie zoeken op de [gedeeltelijke term en patronen met speciale tekens](search-query-partial-matching.md).

Als u een intensief gebruik van Booleaanse Opera tors verwacht, wat waarschijnlijker is in indexen met grote tekst blokken (een inhouds veld of lange beschrijving), test u de query's met de **`searchMode=Any|All`** para meter om de impact van deze instelling te evalueren op Booleaanse Zoek opdrachten.

## <a name="autocomplete-and-suggested-queries"></a>Automatisch aanvullen en voorgestelde query's

[Automatisch aanvullen of voorgestelde resultaten](search-autocomplete-tutorial.md) zijn alternatieven voor **`search`** het activeren van opeenvolgende query aanvragen op basis van een gedeeltelijke teken reeks invoer (na elk teken) in een zoek-als-u-type-ervaring. U kunt **`autocomplete`** en **`suggestions`** para meter samen gebruiken, zoals wordt beschreven in [deze zelf studie](tutorial-csharp-type-ahead-and-suggestions.md), maar u kunt deze niet gebruiken met **`search`** . De volledige voor waarden en voorgestelde query's zijn afgeleid van de inhoud van de index. De engine retourneert nooit een teken reeks of suggestie die niet bestaat in de index. Zie AutoAanvullen [(rest API)](/rest/api/searchservice/autocomplete) en [suggesties (rest API)](/rest/api/searchservice/suggestions)voor meer informatie.

## <a name="filter-search"></a>Zoek filter

Filters worden veel gebruikt in apps die Cognitive Search bevatten. Op toepassings pagina's worden filters vaak gevisualiseerd als facetten in navigatie structuren voor koppelingen voor gebruikers gerichte filters. Filters worden ook intern gebruikt om segmenten van geïndexeerde inhoud beschikbaar te maken. U kunt bijvoorbeeld een zoek pagina initialiseren met een filter voor een product categorie of een taal als een index velden bevat in het Engels en het Frans.

U hebt mogelijk ook filters nodig om een speciaal query formulier aan te roepen, zoals wordt beschreven in de volgende tabel. U kunt een filter gebruiken met een niet-opgegeven zoek opdracht ( **`search=*`** ) of met een query reeks die termen, zinsdelen, Opera tors en patronen bevat.

| Filter scenario | Beschrijving |
|-----------------|-------------|
| Bereik filters | In azure Cognitive Search worden bereik query's gemaakt met behulp van de filter parameter. Zie voor meer informatie en voor beelden [Range filter voor beeld](search-query-simple-examples.md#example-5-range-filters). |
| Geolocatie zoeken | Als een doorzoekbaar veld van het [type EDM. GeographyPoint](/rest/api/searchservice/supported-data-types)is, kunt u een filter expressie maken voor de besturings elementen "dichtbij zoeken" of op basis van een kaart. Velden die geografische zoek acties hebben, bevatten coördinaten. Zie voor meer informatie en een voor beeld een voor beeld van [geo-zoeken](search-query-simple-examples.md#example-6-geo-search). |
| Facetnavigatie | Een facet navigatie structuur wordt instrumenteel in de gebruikers gerichte navigatie wanneer u een filter aanroept in reactie op een `onclick` gebeurtenis in een facet. Als zodanig gaan facetten en filters hand matig aan de slag. Als u facet navigatie toevoegt, hebt u filters nodig om de ervaring te vervolledigen. Zie [een facet filter bouwen](search-filters-facets.md)voor meer informatie. |

> [!NOTE]
> Tekst die wordt gebruikt in een filter expressie wordt niet geanalyseerd tijdens het verwerken van query's. De tekst invoer wordt verondersteld een Verbatim hoofdletter gevoelig teken patroon te zijn dat bij de overeenkomst slaagt of mislukt. Filter expressies worden samengesteld met de [OData-syntaxis](query-odata-filter-orderby-syntax.md) en door gegeven in een **`filter`** para meter in alle *filter* bare velden in uw index. Zie [filters in Azure Cognitive Search](search-filters.md)voor meer informatie.

## <a name="document-look-up"></a>Document opzoeken

In tegens telling tot de eerder beschreven query formulieren, haalt deze één [Zoek document op basis van id](/rest/api/searchservice/lookup-document), zonder bijbehorende index Zoek opdracht of scan. Er wordt slechts één document aangevraagd en geretourneerd. Wanneer een gebruiker een item selecteert in de zoek resultaten, het document ophaalt en een detail pagina met velden invult, wordt een typisch antwoord weer geven en is een document dat het zoekt, de bewerking die dit ondersteunt.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Geavanceerde zoek opdracht: fuzzy, Joker teken, Proximity, regex

Een geavanceerd query formulier is afhankelijk van de volledige lucene-parser en Opera tors die een specifiek query gedrag activeren.

| Query type | Gebruik | Voor beelden en meer informatie |
|------------|--------|------------------------------|
| [Zoek opdracht in veld](query-lucene-syntax.md#bkmk_fields) | **`search`**  bepaalde **`queryType=full`**  | Bouw een samengestelde query-expressie die is gericht op één veld. <br/>[Voor beeld van een zoek opdracht naar een veld](search-query-lucene-examples.md#example-1-fielded-search) |
| [Zoek actie op fuzzy](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** bepaalde **`queryType=full`** | Komt overeen met de termen met een vergelijk bare constructie of spelling. <br/>[Voor beeld van fuzzy zoeken](search-query-lucene-examples.md#example-2-fuzzy-search) |
| [Proximity Search](query-lucene-syntax.md#bkmk_proximity) | **`search`** bepaalde **`queryType=full`** | Hiermee worden zoek termen in een document in de buurt. <br/>[Voor beeld van proximity Search](search-query-lucene-examples.md#example-3-proximity-search) |
| [term versterking](query-lucene-syntax.md#bkmk_termboost) | **`search`** bepaalde **`queryType=full`** | Hiermee wordt een document hoger gerangschikt als het de gestimuleerde term bevat, ten opzichte van andere. <br/>[Voor beeld van een betere term](search-query-lucene-examples.md#example-4-term-boosting) |
| [reguliere expressie zoeken](query-lucene-syntax.md#bkmk_regex) | **`search`** bepaalde **`queryType=full`** | Komt overeen met de inhoud van een reguliere expressie. <br/>[Voor beeld van een reguliere expressie](search-query-lucene-examples.md#example-5-regex) |
|  [Joker teken of voor voegsel zoeken](query-lucene-syntax.md#bkmk_wildcard) | **`search`** para meter met * *_`~`_* of **`?`** , **`queryType=full`**| Komt overeen met een voor voegsel en tilde () `~` of één teken ( `?` ). <br/>[Zoek voorbeeld voor joker tekens](search-query-lucene-examples.md#example-6-wildcard-search) |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de voor beelden voor elke syntaxis voor een beter overzicht van de implementatie van query's. Als u niet bekend bent met zoeken in volledige tekst, bekijkt u wat de query-engine kan hebben.

+ [Voorbeelden van eenvoudige query's](search-query-simple-examples.md)
+ [Voor beelden van Lucene-syntaxis query's voor het maken van geavanceerde query's](search-query-lucene-examples.md)
+ [Hoe zoeken in volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)git