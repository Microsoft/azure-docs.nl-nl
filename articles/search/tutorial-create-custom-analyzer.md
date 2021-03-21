---
title: 'Zelfstudie: Een aangepaste analyse maken'
titleSuffix: Azure Cognitive Search
description: Leer hoe u een aangepaste analyse kunt maken voor het verbeteren van de kwaliteit van zoekresultaten in Azure Cognitive Search.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/29/2021
ms.openlocfilehash: f4bde98cfc772f5a80bb52c2e4bc2f5a9c28c78d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99097654"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>Zelfstudie: Een aangepaste analyse voor telefoonnummers maken

[Analyses](search-analyzers.md) zijn een belangrijk onderdeel van elke zoekoplossing. Om de kwaliteit van de zoekresultaten te verbeteren, is het belangrijk te begrijpen hoe analyses werken en hoe deze resultaten beïnvloeden.

In sommige gevallen, zoals bij een veld met vrije tekst, kunt u door gewoon de juiste [taalanalyse](index-add-language-analyzers.md) te selecteren, de zoekresultaten verbeteren. Voor sommige scenario's, zoals het zorgvuldig zoeken naar telefoonnummers, URL's of e-mails, is het gebruik van aangepaste analyses vereist.

In deze zelfstudie wordt gebruikgemaakt van Postman en [REST API's](/rest/api/searchservice/) van Azure Cognitive Search om:

> [!div class="checklist"]
> * Uit te leggen hoe analyses werken
> * Een aangepaste analyse te definiëren waarmee naar telefoonnummers kan worden gezocht
> * Te testen hoe met aangepaste analyses tekst wordt getokeniseerd
> * Afzonderlijke analyses voor indexeren en zoeken te maken om nog betere resultaten te verkrijgen

## <a name="prerequisites"></a>Vereisten

De volgende services en hulpprogramma's zijn vereist voor deze zelfstudie.

+ [Postman bureaublad-app](https://www.getpostman.com/)
+ [Maak](search-create-service-portal.md) of [vind een bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

## <a name="download-files"></a>Bestanden downloaden

De broncode voor deze zelfstudie bevindt zich in de map [custom-analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) in de GitHub-opslagplaats [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples).

## <a name="1---create-azure-cognitive-search-service"></a>1 - Azure Cognitive Search-service maken

Voor het voltooien van deze zelfstudie hebt u een Azure Cognitive Search-service nodig, die u [in de portal kunt maken](search-create-service-portal.md). U kunt de gratis laag gebruiken om dit voorbeeld te voltooien.

Voor de volgende stap moet u de naam van de zoekservice en de bijbehorende API-sleutel weten. Als u niet zeker weet hoe u deze items kunt vinden, raadpleegt u deze [quickstart](search-create-service-portal.md#get-a-key-and-url-endpoint).


## <a name="2---set-up-postman"></a>2 - Postman instellen

Start vervolgens Postman en importeer de verzameling die u hebt gedownload uit [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples).

U kunt de verzameling importeren, door naar **Bestanden** > **Importeren** te gaan en vervolgens het verzamelingsbestand te selecteren dat u wilt importeren.

Voor elke aanvraag moet u het volgende doen:

1. Vervang `<YOUR-SEARCH-SERVICE>` door de naam van uw zoekservice.

1. Vervang `<YOUR-ADMIN-API-KEY>` door de primaire of de secundaire sleutel van uw zoekservice.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="URL en header voor Postman-aanvraag" border="false":::

Raadpleeg [REST API's van Azure Cognitive Search verkennen](search-get-started-rest.md) als u niet bekend bent met Postman.

## <a name="3---create-an-initial-index"></a>3 - Een initiële index maken

In deze stap maakt u een initiële index, laadt u documenten in de index en voert u een query uit op de documenten om te zien hoe uw eerste zoekopdrachten presteren.

### <a name="create-index"></a>Index maken

We beginnen met het maken van een eenvoudige index met de naam `tutorial-basic-index` die twee velden bevat: `id` en `phone_number`. Omdat er nog geen analyse is gedefinieerd, wordt standaard de analyse `standard.lucene` gebruikt.

De volgende aanvraag wordt verzonden om de index te maken:

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>Gegevens laden

Als volgende gaan we gegevens in de index laden. Het is niet altijd mogelijk om de indeling van de opgenomen telefoonnummers te bepalen, zodat we verschillende indelingen gaan testen. In het ideale geval worden met een zoekoplossing alle overeenkomende telefoonnummers geretourneerd, ongeacht de indeling ervan.

Gegevens worden in de index geladen met behulp van de volgende aanvraag: 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

Nu de gegevens zich in de index bevinden, kunnen we beginnen met zoeken.

### <a name="search"></a>Search

Als u de zoekfunctie intuïtief wilt maken, kunt u er maar beter niet op rekenen dat gebruikers voor query's bepaalde indelingen gebruiken. Een gebruiker zoekt bijvoorbeeld naar `(425) 555-0100` in een van de indelingen die we eerder hebben getoond, en verwacht toch dat er resultaten worden geretourneerd. In deze stap gaan we een aantal voorbeeldquery's testen om te zien hoe deze presteren.

We beginnen met het zoeken naar `(425) 555-0100`:

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

Met deze query worden **drie van vier verwachte resultaten** geretourneerd, maar worden ook **twee niet-verwachte resultaten** geretourneerd:

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

Nu gaan we naar een nummer zoeken zonder enige indeling `4255550100`

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

Deze query presteert nog slechter, omdat er maar **één van vier juiste overeenkomsten** wordt geretourneerd.

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

U bent niet de enige die deze resultaten verwarrend vindt. In de volgende sectie wordt uitgelegd waarom we deze resultaten krijgen te zien.

## <a name="4---debug-search-results"></a>4 - Fouten in zoekresultaten opsporen

U kunt deze zoekresultaten alleen begrijpen als u weet hoe analyses werken. Hier kunnen we de standaardanalyse testen met behulp van de [Tekstanalyse-API](/rest/api/searchservice/test-analyzer) en vervolgens een analyse maken die aan onze eisen voldoet.

### <a name="how-analyzers-work"></a>Hoe analyses werken

Een [analyse](search-analyzers.md) maakt deel uit van de [engine voor zoekopdrachten in volledige tekst](search-lucene-query-architecture.md) en is verantwoordelijk voor het verwerken van tekst in queryreeksen en geïndexeerde documenten. Afhankelijk van het scenario manipuleren verschillende analyses tekst op verschillende manieren. Voor dit scenario gaan we een analyse maken die geschikt is voor telefoonnummers.

Analyses bestaan uit drie onderdelen:

+ [**tekenfilters**](#CharFilters) waarmee afzonderlijke tekens in de invoertekst worden verwijderd of vervangen.
+ een [**tokenizer**](#Tokenizers) waarmee de invoertekst wordt opgesplitst in tokens, die sleutels worden in de zoekindex.
+ [**tokenfilters**](#TokenFilters) waarmee de tokens worden gemanipuleerd die door de tokenizer worden geproduceerd.

In het onderstaande diagram kunt u zien hoe deze drie onderdelen samenwerken om een zin te tokeniseren:

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="Diagram van het analyseproces om een zin te tokeniseren":::

Deze tokens worden vervolgens opgeslagen in een omgekeerde index, waardoor snelle zoekopdrachten in volledige tekst mogelijk zijn.  Met een omgekeerde index kunt u zoeken in volledige tekst toestaan door alle unieke termen die zijn geëxtraheerd tijdens de lexicale analyse, toe te wijzen aan de documenten waarin ze voorkomen. In het onderstaande diagram ziet u een voorbeeld:

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="Voorbeeld van een omgekeerde index":::

Elke zoekopdracht komt neer op het zoeken naar de termen die in de omgekeerde index zijn opgeslagen. Wanneer een gebruiker een query opgeeft:

1. Wordt de query geparseerd en worden de querytermen geanalyseerd.
1. De omgekeerde index wordt vervolgens gescand op documenten die overeenkomende termen bevatten.
1. Ten slotte worden de opgehaalde documenten gerangschikt met behulp van het [vergelijkbaarheidsalgoritme](index-ranking-similarity.md).

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="Diagram van het rangschikken van vergelijkbaarheid in het analyseproces":::

Als de querytermen niet overeenkomen met de termen in de omgekeerde index, worden er geen resultaten geretourneerd. Voor meer informatie over hoe query's werken, raadpleegt u dit artikel over [zoeken in volledige tekst](search-lucene-query-architecture.md).

> [!Note]
> [Query's die uit onvolledige termen bestaan](search-query-partial-matching.md) vormen een belangrijke uitzondering op deze regel. Met deze query's (query met voorvoegsel, jokerteken of die uit een reguliere-expressie bestaat) wordt het lexicale analyseproces omzeild, in tegenstelling tot query's die uit reguliere termen bestaan. Gedeeltelijke termen worden alleen in kleine letters omgezet voordat ze aan overeenkomende termen in de index worden gekoppeld. Als een analyse niet is geconfigureerd om deze typen query's te ondersteunen, ontvangt u vaak onverwachte resultaten omdat er geen overeenkomende termen in de index voorkomen.

### <a name="test-analyzer-using-the-analyze-text-api"></a>Analyse testen met behulp van de Tekstanalyse-API

Azure Cognitive Search biedt een [Tekstanalyse-API](/rest/api/searchservice/test-analyzer) waarmee u analyses kunt testen om inzicht te krijgen in de manier waarop deze tekst verwerken.

De Tekstanalyse-API wordt aangeroepen met de volgende aanvraag:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "text": "(425) 555-0100",
    "analyzer": "standard.lucene"
  }
```

De API retourneert vervolgens een lijst met de tokens die zijn geëxtraheerd uit de tekst. U kunt zien dat Lucene Analyzer, die standaard wordt gebruikt, het telefoonnummer opsplitst in drie afzonderlijke tokens:

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

Omgekeerd wordt het telefoonnummer `4255550100` waarvan de indeling geen enkel leesteken bevat, in één enkele token getokeniseerd.

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

Vergeet niet dat zowel de querytermen als de geïndexeerde documenten worden geanalyseerd. Als u even terugdenkt aan de zoekresultaten uit de vorige stap, wordt het u misschien duidelijk waarom die resultaten werden geretourneerd.

Met de eerste query werden de verkeerde telefoonnummers geretourneerd, omdat een van de termen, `555`, overeenkwam met een van de termen waar we naar op zoek waren. Met de tweede query werd alleen het ene nummer geretourneerd, omdat dit de enige record was die een term bevatte die overeenkwam met `4255550100`.

## <a name="5---build-a-custom-analyzer"></a>5 - Een aangepaste analyse bouwen

Nu we weten waarom deze resultaten werden geretourneerd, gaan we een aangepaste analyse bouwen om de tokeniseringslogica te verbeteren.

Ons doel is om intuïtieve zoekacties te kunnen uitvoeren op telefoonnummers, ongeacht welke indeling de query of geïndexeerde tekenreeks heeft. Dit resultaat bereiken we als we een [tekenfilter](#CharFilters), een [tokenizer](#Tokenizers) en een [tokenfilter](#TokenFilters) opgeven.

<a name="CharFilters"></a>

### <a name="character-filters"></a>Tekenfilters

Tekenfilters worden gebruikt voor het verwerken van tekst voordat deze aan de tokenizer wordt doorgegeven. Tekenfilters worden vaak gebruikt om HTML-elementen uit te filteren of om speciale tekens te vervangen.

Bij telefoonnummers willen we witruimte en speciale tekens verwijderen omdat niet alle telefoonnummerindelingen dezelfde speciale tekens en spaties bevatten.

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

Met het bovenstaande filter worden `-` `(` `)` `+` `.` en spaties verwijderd uit de invoer.

|Invoer|Uitvoer|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Tokenizers

Met tokenizers wordt tekst opgesplitst in tokens en worden tegelijkertijd sommige tekens, zoals leestekens, verwijderd. Vaak is het doel van tokeniseren om een zin op te splitsen in afzonderlijke woorden.

Voor dit scenario gaan we een trefwoordtokenizer gebruiken, `keyword_v2`, omdat we het telefoonnummer willen vastleggen als één enkele term. Dit is overigens niet de enige manier om dit probleem op te lossen. Zie de sectie [Andere manieren](#Alternate) hieronder.

Bij het gebruik van trefwoordtokenizers is de invoertekst gelijk aan de uitvoertekst, maar dan in de vorm van één enkele term.

|Invoer|Uitvoer|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>Tokenfilters

Met tokenfilters worden de tokens die zijn gegenereerd door de tokenizer, uitgefilterd of gewijzigd. Tokenfilters worden vaak gebruikt om alle tekens in kleine letters om te zetten met behulp van kleine-lettertokenfilter. Ook worden tokenfilters vaak gebruikt om stopwoorden, zoals `the`, `and` of `is` uit te filteren.

Voor dit scenario gaan we geen van deze filters gebruiken, maar gaan we een nGram-tokenfilter gebruiken om deelzoekopdrachten voor telefoonnummers mogelijk te maken.

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

Met het [tokenfilter nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html) worden tokens gesplitst in n-grammen van een bepaalde grootte op basis van de parameters `minGram` en `maxGram`.

Voor de telefoonanalyse stellen we `minGram` in op `3` omdat dit de kortste subtekenreeks is waarnaar gebruikers verwacht worden te zoeken. `maxGram` is ingesteld op `20` om ervoor te zorgen dat alle telefoonnummers, zelfs met toestelnummers, in één n-gram passen.

 Het vervelend neveneffect van n-grammen is dat er enkele fout-positieven worden geretourneerd. Dit gaan we in stap 7 oplossen door een afzonderlijke analyse te bouwen voor zoekopdrachten die geen n-gram-tokenfilter bevatten.

|Invoer|Uitvoer|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>Analyse

Nu we over tekenfilters, tokenizers en tokenfilters beschikken kunnen we beginnen met het definiëren van onze analyse.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|Invoer|Uitvoer|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

Zoals u ziet kunnen we nu alle tokens in de uitvoer doorzoeken. Als de query een van deze tokens bevat, wordt het telefoonnummer geretourneerd.

Nu de aangepaste analyse is gedefinieerd, wordt de index opnieuw gegenereerd, zodat de aangepaste analyse in de volgende stap kan worden getest. Ter vereenvoudiging van dit proces wordt met de Postman-verzameling een nieuwe index met de naam `tutorial-first-analyzer` gemaakt met behulp van de analyse die we hebben gedefinieerd.

## <a name="6---test-the-custom-analyzer"></a>6 - De aangepaste analyse testen

Nadat de index is gegenereerd, kunt u de door u gemaakte analyse testen met behulp van de volgende aanvraag:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

U kunt vervolgens de verzameling tokens zien die voortkomen uit het telefoonnummer:

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7 - Een aangepaste analyse voor query's bouwen

Nadat u met de aangepaste analyse een aantal voorbeelden hebt gemaakt op basis van de index, zult u zien dat het aantal relevante overeenkomsten is toegenomen en dat alle overeenkomende telefoonnummers nu worden geretourneerd. Het n-gram-tokenfilter produceert echter ook een aantal fout-positieven. Dit is een veelvoorkomend neveneffect van een n-gram-tokenfilter.

Ter voorkoming van fout-positieven, gaan we een afzonderlijke analyse voor het uitvoeren van query's maken. Deze analyse is dezelfde als de analyse die u eerder al hebt gemaakt, maar dan **zonder** de `custom_ngram_filter`.

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

In de indexdefinitie geven we vervolgens zowel een `indexAnalyzer` als een `searchAnalyzer`op.

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

Nadat u deze wijziging hebt aangebracht, bent u klaar. Genereer de index opnieuw, indexeer de gegevens en test de query's nogmaals om te controleren of de zoekopdracht werkt zoals verwacht. Als u de Postman-verzameling gebruikt, wordt er een derde index gemaakt met de naam `tutorial-second-analyzer`.

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>Andere manieren

De bovenstaande analyse is ontworpen om de flexibiliteit van zoekopdrachten te maximaliseren. Dit gaat echter ten koste gaan van de mogelijkheid om veel, mogelijk onbelangrijke termen in de index te kunnen opslaan.

In het onderstaande voorbeeld ziet u een andere analyse die ook voor deze taak kan worden gebruikt. 

De analyse werkt goed, maar niet voor invoergegevens zoals `14255550100` waarbij het moeilijk is om het telefoonnummer op een logische manier te segmenteren. Het is bijvoorbeeld niet mogelijk om het landnummer `1` van het netnummer `425` te scheiden. Deze discrepantie zou ertoe leiden dat het bovenstaande nummer niet wordt geretourneerd als een gebruiker geen landnummer in de zoekopdracht opneemt.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

In het onderstaande voorbeeld ziet u dat het telefoonnummer is gesplitst in segmenten waar een gebruiker naar verwachting normaal gesproken naar zoekt.

|Invoer|Uitvoer|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

Afhankelijk van uw behoeften kan dit een efficiëntere manier zijn om het probleem te benaderen.

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

Om het eenvoudig te houden werd u in deze zelfstudie gevraagd om drie nieuwe indexen te maken. Het is echter gebruikelijk om tijdens de eerste fase van de ontwikkeling indexen te verwijderen en opnieuw te maken. U kunt een index in de Azure-portal verwijderen of met de volgende API-aanroep:

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>Opgedane kennis

In deze zelfstudie werd het proces voor het maken en testen van een aangepaste analyse getoond. U hebt een index gemaakt, gegevens geïndexeerd en vervolgens een query uitgevoerd op basis van de index om te zien welke zoekresultaten werden geretourneerd. U hebt daarna de Tekstanalyse-API gebruikt om lexicale analyse in werking te zien.

Hoewel de analyse die in deze zelfstudie is gedefinieerd, een eenvoudige oplossing biedt voor het zoeken naar telefoonnummers, kan hetzelfde proces worden gebruikt om een aangepaste telefoonanalyse te bouwen voor elk ander scenario.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project de resources te verwijderen die u niet meer nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling Alle resources of Resourcegroepen in het navigatiedeelvenster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met het maken van een aangepaste analyse, gaan we kijken naar de verschillende filters, tokenizers en analyses die u tot uw beschikking hebt om een rijke zoekervaring te creëren.

> [!div class="nextstepaction"]
> [Aangepaste analyses in Azure Cognitive Search](index-add-custom-analyzers.md)