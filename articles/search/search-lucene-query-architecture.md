---
title: Volledige-tekst query en architectuur van Indexing Engine (lucene)
titleSuffix: Azure Cognitive Search
description: Inspecteert de verwerking van de Lucene-query's en het ophalen van documenten voor zoeken in volledige tekst, met betrekking tot Azure Cognitive Search.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 50a1656fcb92d9777d4a9476ef2a4c1fd2f2efc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002745"
---
# <a name="full-text-search-in-azure-cognitive-search"></a>Zoeken in volledige tekst in azure Cognitive Search

Dit artikel is voor ontwikkel aars die meer inzicht nodig hebben in de manier waarop de zoek opdracht in de volledige tekst van lucene in azure Cognitive Search werkt. Voor tekstquery’s worden in de meeste scenario’s in Azure Cognitive Search soepel de verwachte resultaten geretourneerd, maar zo nu en dan krijgt u mogelijk een resultaat dat niet helemaal klopt. In dergelijke situaties helpt het iets af te weten van de vier stadia van Lucene-queryuitvoering (query’s parseren, lexicale analyse, documentovereenkomsten, scoren). Dit kan u helpen specifieke wijzigingen in de queryparameters of indexconfiguratie te identificeren, die leiden tot het verwachte resultaat. 

> [!Note] 
> Azure Cognitive Search maakt gebruik van Lucene voor zoeken in volledige tekst, maar de integratie van Lucene is niet volledig. U kunt de Lucene-functionaliteit selectief beschikbaar maken en uitbreiden om de scenario's in te scha kelen die belang rijk zijn voor Azure Cognitive Search. 

## <a name="architecture-overview-and-diagram"></a>Architectuur overzicht en diagram

Het verwerken van een zoek opdracht voor volledige tekst begint met het parseren van de query tekst om zoek termen te extra heren. De zoek machine gebruikt een index voor het ophalen van documenten met overeenkomende voor waarden. Afzonderlijke query termen worden soms onderverdeeld in nieuwe formulieren om het net groter te maken ten opzichte van wat kan worden beschouwd als een mogelijke overeenkomst. Vervolgens wordt er een resultaatset gesorteerd op een relevantie score die is toegewezen aan elk individueel overeenkomend document. De objecten boven aan de lijst met geclassificeerde worden geretourneerd naar de aanroepende toepassing.

De uitvoering van query's heeft vier fasen: 

1. Query's parseren 
2. Lexicale analyse 
3. Ophalen van documenten 
4. Scoren 

In het onderstaande diagram ziet u de onderdelen die worden gebruikt voor het verwerken van een zoek opdracht. 

 ![Het schema van de Lucene-query architectuur in azure Cognitive Search][1]


| Belangrijkste onderdelen | Functionele beschrijving | 
|----------------|------------------------|
|**Query-parsers** | Scheid query termen van query operators en maak de query structuur (een query structuur) die naar de zoek machine moet worden verzonden. |
|**Analyses** | Voer een lexicale analyse uit op query termen. Dit proces kan betrekking hebben op het transformeren, verwijderen of uitbreiden van query termen. |
|**Index** | Een efficiënte gegevens structuur die wordt gebruikt voor het opslaan en organiseren van Doorzoek bare termen die zijn geëxtraheerd uit geïndexeerde documenten. |
|**Zoek machine** | Hiermee worden overeenkomende documenten opgehaald en gescoord op basis van de inhoud van de omgekeerde index. |

## <a name="anatomy-of-a-search-request"></a>Anatomie van een zoekaanvraag

Een zoek opdracht is een volledige specificatie van wat er in een resultatenset moet worden geretourneerd. In het eenvoudigste vorm is het een lege query zonder criteria van welke aard dan ook. Een realistischer voor beeld bevat para meters, verschillende query termen, mogelijk op bepaalde velden, met mogelijk een filter expressie en regels voor het ordenen.  

Het volgende voor beeld is een zoek opdracht die u via de [rest API](/rest/api/searchservice/search-documents)kunt verzenden naar Azure Cognitive Search.  

```
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
```

Voor deze aanvraag doet de zoek machine het volgende:

1. Filtert documenten waarvan de prijs ten minste $60 en kleiner is dan $300.
2. Voert de query uit. In dit voor beeld bestaat de zoek query uit zinsdelen en voor waarden: `"Spacious, air-condition* +\"Ocean view\""` (gebruikers voeren doorgaans geen interpunctie tekens in, maar in het voor beeld kunnen we uitleggen hoe analyseers dit verwerken). Voor deze query scant de zoek machine de velden Beschrijving en titel die zijn opgegeven in `searchFields` voor documenten die de weer gave ' ochtend ' bevatten, en ook op de term ' spacious ', of op termen die beginnen met het voor voegsel ' Air-voor waarde '. De `searchMode` para meter wordt gebruikt om een wille keurige term (standaard) of alle waarden te vergelijken, voor gevallen waarbij een term niet expliciet vereist is ( `+` ).
3. Bestelt de resulterende set hotels door nabij de opgegeven geografische locatie, en keert vervolgens terug naar de aanroepende toepassing. 

Het meren deel van dit artikel is het verwerken van de *Zoek query*: `"Spacious, air-condition* +\"Ocean view\""` . Filteren en ordenen vallen buiten het bereik. Zie de [naslag documentatie voor Search API](/rest/api/searchservice/search-documents)voor meer informatie.

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Fase 1: Query's parseren 

Zoals aangegeven, is de query reeks de eerste regel van de aanvraag: 

```
 "search": "Spacious, air-condition* +\"Ocean view\"", 
```

De query-parser scheidt Opera tors (zoals `*` en `+` in het voor beeld) uit zoek termen en ontbouwt de zoek query in *subquery's* van een ondersteund type: 

+ *term query* voor zelfstandige voor waarden (zoals spacious)
+ *woordgroepen query* voor geciteerde voor waarden (zoals de weer gave Oceaan)
+ *voorvoegsel query* voor voor waarden gevolgd door een voorvoegsel operator `*` (zoals airconditioning)

Zie voor een volledige lijst met ondersteunde query typen de [opdracht syntaxis van Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Opera tors die zijn gekoppeld aan een subquery bepalen of de query ' moet zijn ' of ' moet ' zijn ' voldaan om een document als een overeenkomst te worden beschouwd. `+"Ocean view"`Is bijvoorbeeld ' moet ' door de `+` operator. 

De query-parser structureert de subquery's in een *query structuur* (een interne structuur die de query weergeeft) die wordt door gegeven aan de zoek machine. In de eerste fase van het parseren van query's ziet de query structuur er als volgt uit.  

 ![Boole-query Search mode][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Ondersteunde parsers: eenvoudige en volledige lucene 

 Azure Cognitive Search biedt twee verschillende query talen, `simple` (standaard) en `full` . Door de `queryType` para meter in te stellen met uw zoek aanvraag, vertelt u de query-parser welke query taal u kiest, zodat u weet hoe de Opera tors en de syntaxis moeten worden geïnterpreteerd. De [eenvoudige query taal](/rest/api/searchservice/simple-query-syntax-in-azure-search) is intuïtief en robuust, en is vaak geschikt voor het interpreteren van gebruikers invoer zonder dat de verwerking aan de client zijde wordt uitgevoerd. De functie biedt ondersteuning voor query-Opera tors die bekend zijn met Web Search-Engines. De [volledige lucene-query taal](/rest/api/searchservice/lucene-query-syntax-in-azure-search), die u op basis van instelling krijgt `queryType=full` , breidt de standaard eenvoudige query taal uit door ondersteuning toe te voegen voor meer Opera tors en query typen, zoals joker tekens, fuzzy, regex en query's met een veld bereik. Een reguliere expressie die in eenvoudige query syntaxis wordt verzonden, wordt bijvoorbeeld geïnterpreteerd als een query reeks en geen expressie. In de voorbeeld aanvraag in dit artikel wordt gebruikgemaakt van de volledige lucene-query taal.

### <a name="impact-of-searchmode-on-the-parser"></a>De impact van Search mode op de parser 

Een andere zoek aanvraag parameter die van invloed is op parsering is de `searchMode` para meter. Het beheert de standaard operator voor Booleaanse query's: any (standaard) of alle.  

Wanneer `searchMode=any` , de standaard waarde, het spatie scheidings teken tussen spacious en de lucht voorwaarde of ( `||` ), waarbij de voorbeeld query tekst gelijk is aan: 

```
Spacious,||air-condition*+"Ocean view" 
```

Expliciete Opera Tors, zoals `+` in `+"Ocean view"` , zijn niet eenduidig in de constructie van een Boole-query (de term *moet* overeenkomen). Minder duidelijk wordt uitgelegd hoe u de resterende voor waarden interpreteert: spacious en Air-voor waarde. Moet de zoek machine overeenkomen met de weer gave van de oceaan *en* de spacious *en* de airconditioning? Of moet de weer gave van de Oceaan en *een* van de resterende voor waarden worden gevonden? 

`searchMode=any`De zoek machine neemt standaard de uitgebreidere uitleg. Elk veld *moet* overeenkomen met ' of ' semantiek. De eerste query structuur die eerder is geïllustreerd, met de twee ' moet-' bewerkingen, toont de standaard waarde.  

Stel dat we nu hebben ingesteld `searchMode=all` . In dit geval wordt de ruimte geïnterpreteerd als een ' and '-bewerking. Elk van de resterende voor waarden moet aanwezig zijn in het document om in aanmerking te komen als overeenkomst. De resulterende voorbeeld query wordt als volgt geïnterpreteerd: 

```
+Spacious,+air-condition*+"Ocean view"
```

Een gewijzigde query structuur voor deze query zou er als volgt uitzien, waarbij een overeenkomend document het snij punt is van alle drie subquery's: 

 ![Booleaanse query Search mode][3]

> [!Note] 
> Als u kiest `searchMode=any` `searchMode=all` voor wordt een beslissing aangeraden bij het uitvoeren van representatieve query's. Gebruikers die waarschijnlijk Opera tors kunnen bevatten (gebruikelijk bij het zoeken naar document winkels), kunnen resultaten beter intuïtief vinden als ze `searchMode=all` Boole-query constructies informeren. `searchMode`Zie [eenvoudige query syntaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search)voor meer informatie over de Interplay tussen en-Opera tors.

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Fase 2: lexicale analyse 

Lexicale analyses process *term query's* en *woordgroepen query's* nadat de query structuur is gestructureerd. Met een analyse functie worden de tekst invoer van de parser geaccepteerd, wordt de tekst verwerkt en worden vervolgens de tokens teruggestuurd zodat ze in de query structuur kunnen worden opgenomen. 

De gemeen schappelijke vorm van lexicale analyse is een *linguïstische analyse* waarmee query termen worden getransformeerd op basis van regels die specifiek zijn voor een bepaalde taal: 

* Een query term verkleinen naar de hoofd vorm van een woord 
* Niet-essentiële woorden verwijderen (stopwords, zoals "de" of "en" in het Engels) 
* Een samengesteld woord in onderdeel delen opsplitsen 
* Een hoofd letter woord in kleine letters verlagen 

Al deze bewerkingen verwijderen doorgaans verschillen tussen de tekst invoer van de gebruiker en de termen die zijn opgeslagen in de index. Dergelijke bewerkingen gaan verder dan tekst verwerking en vereisen uitgebreide kennis van de taal zelf. Voor het toevoegen van deze laag taal kundige bewustmaking ondersteunt Azure Cognitive Search een lange lijst [taal analyse](/rest/api/searchservice/language-support) van zowel de Lucene als van micro soft.

> [!Note]
> De analyse vereisten kunnen variëren van Mini maal, afhankelijk van uw scenario. U kunt de complexiteit van de lexicale analyse regelen door het selecteren van een van de vooraf gedefinieerde analyse functies of door uw eigen [aangepaste analyse functie](/rest/api/searchservice/Custom-analyzers-in-Azure-Search)te maken. Analyse functies zijn gericht op Doorzoek bare velden en zijn opgegeven als onderdeel van een veld definitie. Zo kunt u de lexicale analyse per veld variëren. Niet opgegeven, de *standaard* -lucene Analyzer wordt gebruikt.

In het vorige voor beeld heeft de eerste query structuur de term ' spacious ' met een hoofd letter ' S ' en een komma die de query-parser interpreteert als een deel van de query term (een komma wordt niet beschouwd als een query taal operator).  

Wanneer de standaard-Analyzer de term verwerkt, wordt er in een kleine letter "Oceaan" en "spacious" weer gegeven en wordt het komma teken verwijderd. De gewijzigde query structuur ziet er als volgt uit: 

 ![Booleaanse query met geanalyseerde termen][4]

### <a name="testing-analyzer-behaviors"></a>Gedrag van Test Analyzer 

Het gedrag van een Analyzer kan worden getest met behulp van de [analyse-API](/rest/api/searchservice/test-analyzer). Geef de tekst op die u wilt analyseren om te zien welke termen worden gegenereerd door de analyse functie. Als u bijvoorbeeld wilt zien hoe de tekst ' Air-condition ' door de standaard-Analyzer wordt verwerkt, kunt u de volgende aanvraag verzenden:

```json
{
    "text": "air-condition",
    "analyzer": "standard"
}
```

De standaard-Analyzer verbreekt de invoer tekst in de volgende twee tokens, waarbij aantekeningen worden gemaakt met kenmerken zoals begin-en eind verschuivingen (gebruikt voor het markeren van treffers) en hun positie (gebruikt voor overeenkomende woord groepen):

```json
{
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
```

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Uitzonde ringen voor lexicale analyse 

Lexicale analyse geldt alleen voor query typen waarvoor volledige voor waarden zijn vereist: een term query of een woordgroepen query. Het is niet van toepassing op query typen met onvolledige voor waarden – voor voegsel query, Joker teken query, regex-query – of een fuzzy query. Deze query typen, met inbegrip van de voorvoegsel query met de term `air-condition*` in het voor beeld, worden rechtstreeks aan de query structuur toegevoegd, waarbij de analyse fase wordt overgeslagen. De enige trans formatie die wordt uitgevoerd voor query termen van deze typen is lowercasing.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Fase 3: ophalen van documenten 

Het ophalen van documenten heeft betrekking op het zoeken van document met overeenkomende termen in de index. Deze fase wordt het beste geïnterpreteerd door een voor beeld. Laten we beginnen met een Hotels-index met het volgende eenvoudige schema: 

```json
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
```

Verder wordt ervan uitgegaan dat deze index de volgende vier documenten bevat: 

```json
{
    "value": [
        {
            "id": "1",
            "title": "Hotel Atman",
            "description": "Spacious rooms, ocean view, walking distance to the beach."
        },
        {
            "id": "2",
            "title": "Beach Resort",
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."
        },
        {
            "id": "3",
            "title": "Playa Hotel",
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },
        {
            "id": "4",
            "title": "Ocean Retreat",
            "description": "Quiet and secluded"
        }
    ]
}
```

**Hoe termen worden geïndexeerd?**

Als u meer inzicht wilt krijgen in het ophalen, kunt u een aantal basis beginselen van het indexeren weten. De opslag eenheid is een omgekeerde index, één voor elk doorzoekbaar veld. In een omgekeerde index is een gesorteerde lijst met alle termen uit alle documenten. Elke term wordt toegewezen aan de lijst met documenten waarin deze plaatsvindt, zoals duidelijk in het onderstaande voor beeld.

Voor het produceren van de voor waarden in een omgekeerde index voert de zoek machine een lexicale analyse uit op de inhoud van documenten, vergelijkbaar met wat er gebeurt tijdens de verwerking van query's:

1. *Tekst invoer* wordt door gegeven aan een analyse, kleine letters, verwijderd uit interpunctie, enzovoort, afhankelijk van de configuratie van de analyse functie. 
2. *Tokens* zijn de uitvoer van lexicale analyse.
3. De *voor waarden* worden toegevoegd aan de index.

Het is gebruikelijk, maar niet vereist, voor het gebruik van dezelfde analyse functies voor zoek-en indexerings bewerkingen zodat de query termen in de index meer lijken op voor waarden.

> [!Note]
> Met Azure Cognitive Search kunt u verschillende analyse functies opgeven voor indexering en zoek opdrachten via extra `indexAnalyzer` en `searchAnalyzer` veld parameters. Als u geen waarde opgeeft, wordt de analyseset met de `analyzer` eigenschap gebruikt voor het indexeren en zoeken.  

**Omgekeerde index voor voorbeeld documenten**

Als u naar ons voor beeld terugkeert, ziet u in het veld **titel** de omgekeerde index:

| Termijn | Document lijst |
|------|---------------|
| Atman | 1 |
| Beach | 2 |
| Hotel | 1, 3 |
| Oceaan | 4  |
| playa | 3 |
| Hotel | 3 |
| opnieuw behandelen | 4 |

In het veld titel wordt alleen *Hotel* weer gegeven in twee documenten: 1, 3.

De index voor het veld **Beschrijving** is als volgt:

| Termijn | Document lijst |
|------|---------------|
| massa | 3
| en | 4
| Beach | 1
| waarbij | 3
| denkt | 3
| afstand | 1
| Réunion | 2
| kaua ʻ i | 2
| staan | 2
| noord | 2
| Oceaan | 1, 2, 3
| van | 2
| op |2
| stille | 4
| kleed  | 1, 3
| secluded | 4
| eland | 2
| spacious | 1
| het | 1, 2
| tot | 1
| weergave | 1, 2, 3
| Walking | 1
| wordt uitgevoerd met | 3


**Overeenkomende query termen op basis van geïndexeerde voor waarden**

Op basis van de omgedraaide indices hierboven gaan we terug naar de voorbeeld query en bekijken hoe overeenkomende documenten worden gevonden voor de voorbeeld query. De uiteindelijke query structuur ziet er als volgt uit: 

 ![Booleaanse query met geanalyseerde termen][4]

Tijdens de uitvoering van de query worden afzonderlijke query's uitgevoerd voor de Doorzoek bare velden afzonderlijk. 

+ De TermQuery, ' spacious ', komt overeen met document 1 (Hotel Atman). 

+ De PrefixQuery, "Air-voor waarde *", komt niet overeen met documenten. 

  Dit is een gedrag dat ontwikkel aars soms congaat. Hoewel de term airconditioning in het document bestaat, wordt deze in twee termen gesplitst door de standaard analyse functie. Roep op dat voorvoegsel query's, die gedeeltelijke voor waarden bevatten, niet worden geanalyseerd. Daarom worden termen met het voor voegsel ' Air-voor waarde ' opgezocht in de omgekeerde index en niet gevonden.

+ De PhraseQuery, "weer gave van de Oceaan", zoekt naar de termen "Oceaan" en "weer gave" en controleert de nabijheid van de voor waarden in het oorspronkelijke document. De documenten 1, 2 en 3 komen overeen met deze query in het veld Beschrijving. Document 4 heeft de term Oceaan in de titel, maar wordt niet als een overeenkomst beschouwd, omdat we zoeken naar de ' weer gave ' Oceaan ' in plaats van afzonderlijke woorden. 

> [!Note]
> Een zoek opdracht wordt onafhankelijk uitgevoerd voor alle Doorzoek bare velden in de Azure Cognitive Search-index, tenzij u de velden beperkt die zijn ingesteld met de `searchFields` para meter, zoals wordt geïllustreerd in de zoek opdracht voor beeld. Documenten die overeenkomen met een van de geselecteerde velden, worden geretourneerd. 

In het geheel, voor de query in kwestie, zijn de documenten die overeenkomen 1, 2, 3. 

## <a name="stage-4-scoring"></a>Fase 4: Score  

Elk document in een zoek resultatenset krijgt een relevantie score. De functie van de relevantie score is het rangschikken van de documenten die het beste een gebruikers vraag beantwoorden, zoals wordt aangegeven door de zoek opdracht. De score wordt berekend op basis van de statistische eigenschappen van de voor waarden die overeenkomen. De kern van de Score formule is [TF/IDF (term frequentie-inverse document frequentie)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). In query's met zeldzame en algemene termen leidt TF/IDF de resultaten te verhogen die de zeldzame term bevatten. In een hypothetische index met alle Wikipedia-artikelen, van documenten die overeenkomen met de query van *de Voorzitter*, worden documenten die voldoen aan de *Voorzitter* , beschouwd als meer relevant dan documenten die overeenkomen met *de*.


### <a name="scoring-example"></a>Voor beeld van Score

Haal de drie documenten op die overeenkomen met onze voorbeeld query:

```
search=Spacious, air-condition* +"Ocean view"  
```

```json
{
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
```

Document 1 komt overeen met de query aanbevolen omdat zowel de term *spacious* als de vereiste woordgroepen Oceaan in het veld Beschrijving worden *weer gegeven* . De volgende twee documenten komen alleen overeen met de *weer gave* van de woordgroepen Oceaan. Mogelijk is het verrassende dat de relevantie score voor document 2 en 3 afwijkt, ook al zijn ze op dezelfde manier overeenkomen met de query. De Score formule bevat meer onderdelen dan alleen TF/IDF. In dit geval is document 3 een iets hogere score toegewezen, omdat de beschrijving korter is. Meer informatie over de [praktische Score formule van Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) om te begrijpen hoe veld lengte en andere factoren van invloed kunnen zijn op de relevantie score.

Sommige query typen (joker tekens, voor voegsel, regex) dragen altijd bij aan een constante score voor de algehele document Score. Dit maakt het mogelijk om overeenkomsten te vinden in de resultaten van de query uitbreiding, maar zonder dat dit van invloed is op de rang schikking. 

Een voor beeld illustreert waarom dit van belang is. Zoek opdrachten met Joker tekens, inclusief voorvoegsel zoekacties, zijn dubbel zinnig per definitie, omdat de invoer een gedeeltelijke teken reeks is met mogelijke overeenkomsten voor een zeer groot aantal ongelijksoortige voor waarden (Overweeg een invoer van "Tour *", met overeenkomsten die zijn gevonden op ' rond leidingen ', ' Tourettes ' en ' Tourmaline '). Gezien de aard van deze resultaten is er geen enkele manier om redelijkerwijs te afleiden welke termen waardevoler zijn dan andere. Daarom negeren we de term frequenties wanneer de Score resulteert in query's van het type Joker teken, voor voegsel en regex. In een zoek opdracht met meerdere delen die gedeeltelijke en volledige voor waarden bevat, worden resultaten van de gedeeltelijke invoer opgenomen met een constante Score om te voor komen dat er onverwacht overeenkomende overeenkomsten zijn.

### <a name="score-tuning"></a>Score afstemmen

Er zijn twee manieren voor het afstemmen van relevantie scores in azure Cognitive Search:

1. **Score profielen** verhogen documenten in de geclassificeerde lijst met resultaten op basis van een set regels. In ons voor beeld kunnen we documenten beschouwen die overeenkomen met het veld titel die relevant zijn voor documenten die overeenkomen met het veld Beschrijving. Daarnaast kunnen we documenten met een lagere prijs promoten als in onze index een prijs veld voor elk hotel voor komt. Meer informatie over het [toevoegen van Score profielen aan een zoek index.](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Term versterking** (alleen beschikbaar in de volledige lucene-query syntaxis) biedt een Boosting-operator `^` die kan worden toegepast op elk deel van de query structuur. In ons voor beeld, in plaats van te zoeken op het voor voegsel van de *lucht* \* , kan er een zoek opdracht naar de voor *waarden* voor de airconditioning of het voor voegsel worden gezocht, maar documenten die overeenkomen met de exacte term, worden verhoogd door Boost toe te passen op de term query: * lucht voorwaarde ^ 2 | | lucht conditie * *. Meer informatie over het verbeteren van de [term](/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Score in een gedistribueerde index

Alle indexen in azure Cognitive Search worden automatisch gesplitst in meerdere Shards, zodat we de index snel over meerdere knoop punten kunnen distribueren tijdens de service omhoog of omlaag schalen. Wanneer een zoek aanvraag wordt verzonden, wordt deze voor elke Shard onafhankelijk van elkaar verzonden. De resultaten van elke Shard worden vervolgens samengevoegd en op Score gesorteerd (als er geen andere volg orde is gedefinieerd). Het is belang rijk om te weten dat de functie voor de score voor de frequentie van query termen overeenkomt met de inverse document frequentie in alle documenten in de Shard, niet op alle Shards!

Dit betekent dat een relevantie score *kan* verschillen voor identieke documenten als ze zich op verschillende Shards bevinden. Gelukkig kunnen dergelijke verschillen worden verwijderd omdat het aantal documenten in de index groeit vanwege een meer gelijkmatige verdeling van de term. Het is niet mogelijk om bij te gaan op welke Shard een bepaald document wordt geplaatst. Als er echter een document sleutel wordt gewijzigd, wordt deze altijd toegewezen aan dezelfde Shard.

In het algemeen is de document score niet het beste kenmerk voor het best Ellen van documenten als de stabiliteit van de order belang rijk is. Als er bijvoorbeeld twee documenten met een identieke Score worden opgegeven, is er geen garantie dat er een eerste wordt weer gegeven in de volgende uitvoeringen van dezelfde query. De document Score mag alleen een algemene zin van document relevantie bieden ten opzichte van andere documenten in de resultatenset.

## <a name="conclusion"></a>Conclusie

Het slagen van Internet zoekprogramma's heeft de verwachtingen voor het zoeken in volledige tekst over persoonlijke gegevens gepromoveerd. Voor vrijwel elk soort Zoek ervaring verwachten we nu dat de engine onze intentie begrijpt, zelfs wanneer de termen verkeerd zijn gespeld of onvolledig zijn. We kunnen zelfs verwachten dat overeenkomsten zijn gebaseerd op de bijna overeenkomende termen of synoniemen die we nooit daad werkelijk hebben opgegeven.

In het kader van technische Beschik baarheid is zoeken in volledige tekst zeer complex, waardoor een geavanceerde taal kundige analyse nodig is en een systematische aanpak voor het verwerken van query termen waarbij een relevant resultaat wordt afgedwongen, uitgevouwen en getransformeerd. Gezien de inherente complexen zijn er veel factoren die van invloed kunnen zijn op het resultaat van een query. Daarom is het investeren van de tijd om inzicht te krijgen in de mechanismen voor zoeken in volledige tekst, om te voor komen dat er onverwachte resultaten kunnen worden gebruikt.  

In dit artikel wordt gezocht in volledige tekst in de context van Azure Cognitive Search. We hopen dat u voldoende achtergrond hebt om mogelijke oorzaken en oplossingen te herkennen voor het adresseren van veelvoorkomende query problemen. 

## <a name="next-steps"></a>Volgende stappen

+ Bouw de voor beeld-index, probeer verschillende query's uit en Bekijk de resultaten. Zie [een index maken en er query's op uitvoeren in de portal](search-get-started-portal.md#query-index)voor instructies.

+ Probeer extra query syntaxis uit de sectie voor beeld van [Zoeken in documenten](/rest/api/searchservice/search-documents#bkmk_examples) of van een [eenvoudige query syntaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search) in Search Explorer in de portal.

+ Bekijk [Score profielen](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) als u de rang schikking in uw zoek toepassing wilt aanpassen.

+ Meer informatie over het Toep assen van [taalspecifieke lexicale analyse](/rest/api/searchservice/language-support)functies.

+ [Aangepaste analyse functies configureren](/rest/api/searchservice/custom-analyzers-in-azure-search) voor een minimale verwerking of gespecialiseerde verwerking van specifieke velden.

## <a name="see-also"></a>Zie ook

[REST API voor documenten zoeken](/rest/api/searchservice/search-documents) 

[Vereenvoudigde querysyntaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Volledige Lucene-querysyntaxis](/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Zoekresultaten verwerken](./search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png