---
title: Vereenvoudigde querysyntaxis
titleSuffix: Azure Cognitive Search
description: Verwijzing voor de eenvoudige query syntaxis die wordt gebruikt voor Zoek opdrachten in volledige tekst in azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516577"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Eenvoudige query syntaxis in azure Cognitive Search

Azure Cognitive Search implementeert twee op lucene gebaseerde query talen: [eenvoudige query-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) en de [lucene query-parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). De eenvoudige parser is flexibeler en probeert een aanvraag te interpreteren, zelfs als deze niet perfect is samengesteld. Als gevolg van deze flexibiliteit is dit de standaard instelling voor query's in azure Cognitive Search.

De eenvoudige syntaxis wordt gebruikt voor query-expressies die zijn door gegeven in de **`search`** para meter van een aanvraag van een [Zoek document (rest API)](/rest/api/searchservice/search-documents) , niet te verwarren met de [OData-syntaxis](query-odata-filter-orderby-syntax.md) die wordt gebruikt voor de [**`$filter`**](search-filters.md) and- [**`$orderby`**](search-query-odata-orderby.md) expressies in dezelfde aanvraag. OData-para meters hebben een andere syntaxis en regels voor het maken van query's, teken reeksen, enzovoort.

Hoewel de eenvoudige parser is gebaseerd op de klasse [Apache Lucene Simple query parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , wordt met de implementatie in cognitive Search fuzzy Search uitgesloten. Als u [fuzzy Search](search-query-fuzzy.md)wilt gebruiken, moet u rekening houden met de alternatieve [volledige lucene-query syntaxis](query-lucene-syntax.md) .

## <a name="example-simple-syntax"></a>Voor beeld (eenvoudige syntaxis)

Hoewel **`queryType`** hieronder is ingesteld, is dit de standaard instelling en kan worden wegge laten, tenzij u een ander type wilt herstellen. Het volgende voor beeld is een zoek opdracht met onafhankelijke termen, met een vereiste dat alle overeenkomende documenten ' pool ' bevatten.

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

De **`searchMode`** para meter is relevant in dit voor beeld. Wanneer Boole-Opera tors zich op de query bevinden, moet u in het algemeen instellen `searchMode=all` dat *alle* criteria overeenkomen. Als dat niet het geval is, kunt u de standaard instelling gebruiken `searchMode=any` .

Zie [eenvoudige query syntaxis voorbeelden](search-query-simple-examples.md)voor meer voor beelden. Zie [documenten zoeken (rest API)](/rest/api/searchservice/Search-Documents)voor meer informatie over de query aanvraag en-para meters.

## <a name="keyword-search-on-terms-and-phrases"></a>Zoeken op tref woorden op termen en zinsdelen

Teken reeksen die worden door gegeven aan de **`search`** para meter, kunnen termen of zinsdelen bevatten in elke ondersteunde taal, Booleaanse Opera Tors, prioriteits operatoren, joker tekens of voor voegsel voor ' starten met ' query's, Escape tekens en URL-coderings tekens. De **`search`** para meter is optioneel. Niet opgegeven, zoeken ( `search=*` of `search=" "` ) retourneert de bovenste 50-documenten in een wille keurige (niet-gerangte) volg orde.

+ Een *term zoekopdracht* is een query van een of meer voor waarden, waarbij een van de voor waarden als een overeenkomst wordt beschouwd.

+ Een *woordgroepen zoekopdracht* is een exacte woord groep tussen aanhalings tekens `" "` . Als u bijvoorbeeld ```Roach Motel``` (zonder aanhalings tekens) zoekt naar documenten met ```Roach``` en/of ```Motel``` ergens in een wille keurige volg orde, ```"Roach Motel"``` (met aanhalings tekens), komen alleen documenten overeen met die hele woord groep samen en in die volg orde (de lexicale analyse is nog steeds van toepassing). 

  Afhankelijk van de zoek-client, moet u mogelijk de aanhalings tekens in een zoek opdracht zoeken. In postman in een POST-aanvraag zou bijvoorbeeld een woord groep zoeken in `"Roach Motel"` de hoofd tekst van de aanvraag worden opgegeven als `"\"Roach Motel\""` .

Standaard worden alle termen of zinsdelen die in de para meter worden door gegeven, **`search`** lexicale analyse. Zorg ervoor dat u bekend bent met het tokenisatie gedrag van de Analyzer die u gebruikt. Wanneer query resultaten onverwacht zijn, kan de reden worden getraceerd op basis van de tijds duur van de query.

Alle tekst met een of meer voor waarden wordt beschouwd als een geldig start punt voor het uitvoeren van query's. Azure Cognitive Search komt overeen met documenten die een of meer van de voor waarden bevatten, inclusief eventuele variaties die tijdens de analyse van de tekst zijn gevonden.

Net zoals dit klinkt, is er één aspect van de uitvoering van query's in azure Cognitive Search die *mogelijk* onverwachte resultaten oplevert, in plaats van de zoek resultaten te verminderen, omdat meer voor waarden en Opera tors worden toegevoegd aan de invoer teken reeks. Of deze uitbrei ding daad werkelijk plaatsvindt, is afhankelijk van de opname van een NOT-operator, gecombineerd met een **`searchMode`** parameter instelling die bepaalt hoe niet wordt geïnterpreteerd in termen van en of gedrag. Zie voor meer informatie de operator NOT onder [Booleaanse Opera tors](#boolean-operators).

## <a name="boolean-operators"></a>Booleaanse operatoren

U kunt Booleaanse Opera tors insluiten in een query reeks om de nauw keurigheid van een overeenkomst te verbeteren. In de eenvoudige syntaxis zijn Booleaanse Opera tors op basis van tekens. Tekst operators, zoals het woord en, worden niet ondersteund.

| Teken | Voorbeeld | Gebruik |
|----------- |--------|-------|
| `+` | `pool + ocean` | Een en-bewerking. Bijvoorbeeld, `pool + ocean` bepaalt dat een document beide termen moet bevatten.|
| `|` | `pool | ocean` | Een of-bewerking vindt een overeenkomst wanneer een van beide termen wordt gevonden. In het voor beeld retourneert de query-engine een overeenkomst op documenten met `pool` of `ocean` of beide. Omdat of de standaard operator voor samen voegen is, kunt u dit ook laten verlopen, zodat `pool ocean` het equivalent is van  `pool | ocean` .|
| `-` | `pool – ocean` | Bij een NOT-bewerking worden overeenkomsten geretourneerd voor documenten die de term uitsluiten. <br/><br/>Om het verwachte gedrag voor een niet-expressie op te halen, kunt u overwegen om **`searchMode=all`** de aanvraag in te stellen. Als u dit niet doet, krijgt u, onder de standaard instelling, **`searchMode=any`** overeenkomsten op het `pool` plus teken voor alle documenten die niet bevatten `ocean` . Dit kan veel documenten zijn. De **`searchMode`** para meter voor een query aanvraag bepaalt of een term met de operator Not ANDed of ORed is met andere voor waarden in de query (ervan uitgaande dat er geen `+` operator is of `|` de andere voor waarden). Met **`searchMode=all`** deze instelling wordt de nauw keurigheid van query's verhoogd door minder resultaten op te nemen en standaard-wordt geïnterpreteerd als "en niet". <br/><br/>Houd bij het bepalen van een **`searchMode`** instelling rekening met de gebruikers interactie patronen voor query's in verschillende toepassingen. Gebruikers die op zoek zijn naar informatie, hebben waarschijnlijk vaker een operator in een query opgenomen, in tegens telling tot e-commerce sites met meer ingebouwde navigatie structuren. |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>Voorvoegsel query's

Voor de query's ' begint met ' voegt u een achtervoegsel operator ( `*` ) toe als tijdelijke aanduiding voor de rest van een term. Een voorvoegsel query moet beginnen met ten minste één alfanumeriek teken voordat u de operator suffix kunt toevoegen.

| Teken | Voorbeeld | Gebruik |
|----------- |--------|-------|
| `*` | `lingui*` komt overeen met "linguïstische" of "Linguini" | Het sterretje ( `*` ) vertegenwoordigt een of meer tekens met een wille keurige lengte, waarbij het hoofdletter gebruik wordt genegeerd.  |

Net als bij filters zoekt een voorvoegsel query naar een exacte overeenkomst. Zo is er geen relevantie score (alle resultaten krijgen een zoek Score van 1,0). Houd er rekening mee dat voorvoegsel query's langzaam kunnen zijn, met name als de index groot is en het voor voegsel uit een klein aantal tekens bestaat. Een alternatieve methodologie, zoals Edge n-gram tokening, kan sneller worden uitgevoerd.

Eenvoudige syntaxis biedt alleen ondersteuning voor voor voegsels. Gebruik de [volledige lucene-syntaxis voor Zoek opdrachten met Joker tekens](query-lucene-syntax.md#bkmk_wildcard)voor achtervoegsels of infix die overeenkomen met het einde of midden van een term.

## <a name="escaping-search-operators"></a>Zoek operatoren voor Escapes  

In de eenvoudige syntaxis bevatten Zoek operatoren de volgende tekens: `+ | " ( ) ' \`  

Als een van deze tekens deel uitmaakt van een token in de index, kunt u deze door een voor voegsel op te maken met één back slash ( `\` ) in de query. Stel dat u een aangepaste Analyzer hebt gebruikt voor de volledige term-tokening en de index bevat de teken reeks "luxe en Hotel". Als u een exacte overeenkomst voor dit token wilt krijgen, voegt u een escape-teken in: `search=luxury\+hotel` .

Er zijn twee uitzonde ringen op deze regel, waar Escapes niet nodig is om dingen eenvoudig te maken voor de meest voorkomende gevallen:  

+ De operator NOT `-` moet alleen worden ontsnapd als dit het eerste teken na een spatie is. Als de `-` in het midden wordt weer gegeven (bijvoorbeeld in `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ), kunt u de Escapes overs Laan.

+ De operator achtervoegsel `*` moet alleen worden ontsnapd als dit het laatste teken vóór een spatie is. Als de `*` in het midden wordt weer gegeven (bijvoorbeeld in `4*4=16` ), is er geen escape-tekens nodig.

> [!NOTE]  
> Standaard worden woorden op afbreek streepjes, spaties, ampersands en andere tekens tijdens de [lexicale analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis)verwijderd en verbroken. Als u wilt dat speciale tekens in de query reeks blijven staan, hebt u mogelijk een analyse programma nodig dat deze in de index behoudt. Enkele keuze mogelijkheden zijn micro soft Natural [Language-analyse](index-add-language-analyzers.md)functies, waarmee woorden met een afbreek streepje worden behouden, of een aangepaste analyse functie voor complexere patronen. Zie voor meer informatie [gedeeltelijke termen, patronen en speciale tekens](search-query-partial-matching.md).

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Onveilige en gereserveerde tekens in Url's coderen

Zorg ervoor dat alle onveilige en gereserveerde tekens worden gecodeerd in een URL. Bijvoorbeeld, ' # ' is een onveilig teken, omdat het een fragment/anker-id in een URL is. Het teken moet worden gecodeerd `%23` als het wordt gebruikt in een URL. ' & ' en ' = ' zijn voor beelden van gereserveerde tekens wanneer ze para meters opwaarderen en waarden opgeven in azure Cognitive Search. Zie [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt)voor meer informatie.

Onveilige tekens zijn ``" ` < > # % { } | \ ^ ~ [ ]`` . Gereserveerde tekens zijn `; / ? : @ = + &` .

## <a name="special-characters"></a>Speciale tekens

In sommige gevallen wilt u mogelijk zoeken naar een speciaal teken, zoals een ' ❤ ' Emoji of het teken ' € '. Zorg er in dat geval voor dat de analyse die u gebruikt, deze tekens niet filtert. Met de Standard Analyzer worden veel speciale tekens omzeild, met uitzonde ring van de index.

Analyse functies die Tokenize speciale tekens bevatten, zijn onder andere de ' White '-analyse functie, waarbij alle teken reeksen die worden gescheiden door spaties, worden behandeld als tokens (dus de teken reeks "❤" wordt beschouwd als een token). Daarnaast zou een taal analyse zoals micro soft English Analyzer (en micro soft) de teken reeks "€" als een token. U kunt [een analyse programma testen](/rest/api/searchservice/test-analyzer) om te zien welke tokens worden gegenereerd voor een bepaalde query.

Wanneer u Unicode-tekens gebruikt, moet u ervoor zorgen dat symbolen op de juiste wijze in de query-URL staan (bijvoorbeeld voor ' ❤ ' zou de escape-reeks gebruiken `%E2%9D%A4+` ). Postman doet deze vertaling automatisch.  

## <a name="precedence-grouping"></a>Prioriteit (groepering)

U kunt haakjes gebruiken om subquery's te maken, inclusief Opera tors in de instructie van de haak. Zoekt bijvoorbeeld naar `motel+(wifi|luxury)` documenten met de term ' Motel ' en ' WiFi ' of ' luxe ' (of beide).

## <a name="query-size-limits"></a>Limieten voor query grootte

Als uw toepassing Zoek query's programmatisch genereert, raden we u aan om deze zodanig te ontwerpen dat er geen query's van een ongebonden grootte worden gegenereerd. 

+ Voor GET mag de lengte van de URL niet groter zijn dan 8 KB.

+ Voor POST (en een andere aanvraag), waarbij de hoofd tekst van de aanvraag bevat `search` en andere para meters, zoals `filter` en `orderby` , de maximale grootte is 16 MB, waarbij het maximum aantal componenten in `search` (expressies gescheiden door en, of, enzovoort) 1024 is. Er is ook een limiet van ongeveer 32 KB op de grootte van een afzonderlijke term in een query. Zie [API-aanvraag limieten](search-limits-quotas-capacity.md#api-request-limits)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u query's programmatisch wilt maken, raadpleegt u [Zoeken in volledige tekst in Azure Cognitive Search](search-lucene-query-architecture.md) om inzicht te krijgen in de stadia van het verwerken van query's en de implicaties van tekst analyse.

U kunt ook de volgende artikelen raadplegen voor meer informatie over het bouwen van query's:

+ [Query voorbeelden voor eenvoudige Zoek opdrachten](search-query-simple-examples.md)
+ [Query voorbeelden voor volledige lucene-Zoek opdrachten](search-query-lucene-examples.md)
+ [REST API voor documenten zoeken](/rest/api/searchservice/Search-Documents)
+ [Lucene-query syntaxis](query-lucene-syntax.md)
+ [Syntaxis van de expressie filter en Select (OData)](query-odata-filter-orderby-syntax.md)