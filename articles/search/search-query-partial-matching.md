---
title: Gedeeltelijke termen, patronen en speciale tekens
titleSuffix: Azure Cognitive Search
description: Gebruik joker tekens, regex en voorvoegsel query's die overeenkomen met hele of gedeeltelijke voor waarden in een Azure Cognitive Search query-aanvraag. U kunt aan de hand van de volledige query syntaxis en aangepaste analyse functies gebruikmaken van de meest overeenkomende patronen die speciale tekens bevatten.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 2e2625fff802e71f797bf6970e763f2bf11c393e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584173"
---
# <a name="partial-term-search-and-patterns-with-special-characters-hyphens-wildcard-regex-patterns"></a>Zoeken op gedeeltelijke termen en patronen met speciale tekens (afbreek streepjes, joker tekens, regex, patronen)

Een *gedeeltelijke zoek opdracht* verwijst naar query's die bestaan uit termen fragmenten, in plaats van een hele term, u hebt alleen het begin, het midden of het einde van de term (ook wel voor voegsel-, infix-of achtervoegsel query's genoemd). Een gedeeltelijke zoek opdracht kan bestaan uit een combi natie van fragmenten, vaak met speciale tekens, zoals afbreek streepjes, streepjes of slashes die deel uitmaken van de query teken reeks. Veelvoorkomend gebruik: cases bevatten onderdelen van een telefoon nummer, URL, codes of afgebroken samengestelde woorden.

Zoeken op gedeeltelijke termen en query teken reeksen die speciale tekens bevatten, kunnen problematisch zijn als de index geen tokens in de verwachte indeling heeft. Tijdens de [lexicale analyse fase](search-lucene-query-architecture.md#stage-2-lexical-analysis) van het indexeren (uitgaande van de standaard-Analyzer) worden speciale tekens verwijderd, worden samengestelde woorden opsplitst en wordt witruimte gewist. Dit kan ertoe leiden dat query's mislukken wanneer er geen overeenkomst wordt gevonden. Bijvoorbeeld: een telefoon nummer zoals `+1 (425) 703-6214` (tokend as `"1"` ,, `"425"` `"703"` ,) wordt `"6214"` niet weer gegeven in een `"3-62"` query omdat die inhoud niet echt in de index voor komt. 

De oplossing is het aanroepen van een Analyzer tijdens het indexeren waarbij een volledige teken reeks wordt bewaard, inclusief spaties en speciale tekens, zodat u de spaties en tekens in de query teken reeks kunt opnemen. Op dezelfde manier kunt u met een volledige teken reeks die niet is getokend in kleinere delen, patroon vergelijking voor ' begint met ' of ' eindigt met '-query's, waarbij het patroon dat u opgeeft kan worden geëvalueerd op basis van een term die niet door lexicale analyse wordt getransformeerd. Het maken van een aanvullend veld voor een onvolledige teken reeks, plus het gebruik van een analyse voor het behoud van inhoud die volledige-termijn tokens levert, is de oplossing voor zowel patroon vergelijking als voor overeenkomende query reeksen die speciale tekens bevatten.

> [!TIP]
> Als u bekend bent met postman en REST Api's, [downloadt u de query-voor beelden-verzameling voor het](https://github.com/Azure-Samples/azure-search-postman-samples/) opvragen van gedeeltelijke voor waarden en speciale tekens die in dit artikel worden beschreven.

## <a name="about-partial-term-search"></a>Over het doorzoeken van de gedeeltelijke term

Azure Cognitive Search scans voor hele tokens in de index en er wordt geen overeenkomst met een gedeeltelijke term gevonden tenzij u de tijdelijke aanduidingen voor joker tekens (en) opneemt `*` `?` , of de query opmaken als een reguliere expressie. Gedeeltelijke voor waarden zijn opgegeven met behulp van de volgende technieken:

+ [Reguliere-expressie query's](query-lucene-syntax.md#bkmk_regex) kunnen een reguliere expressie zijn die geldig is onder Apache Lucene. 

+ [Joker operators met voorvoegsel matching](query-simple-syntax.md#prefix-search) verwijzen naar een algemeen herkend patroon dat het begin van een term, gevolgd door `*` of `?` achtervoegsel Opera tors bevat, zoals `search=cap*` overeenkomt met ' Cap'n Jack afgebakend Inn ' of ' gacc Capital '. Het voor voegsel voor het vergelijken van voor voegsels wordt ondersteund in de eenvoudige en volledige lucene-query syntaxis.

+ [Met het Joker teken met infix en achtervoegsel](query-lucene-syntax.md#bkmk_wildcard) `*` worden de `?` Opera tors in of aan het begin van een term geplaatst en wordt een reguliere expressie syntaxis vereist (waarbij de expressie wordt Inge sloten met slashes). De query teken reeks ( `search=/.*numeric*./` ) retourneert bijvoorbeeld resultaten op ' alfanumeriek ' en ' alfanumeriek ' als achtervoegsel-en infix-overeenkomsten.

Voor een gedeeltelijke term of patroon zoekopdracht en enkele andere query formulieren zoals fuzzy zoeken, worden analyse functies niet gebruikt op het moment van de query. Voor deze query formulieren, die door de parser wordt gedetecteerd door de aanwezigheid van Opera tors en scheidings tekens, wordt de query teken reeks door gegeven aan de engine zonder lexicale analyse. Voor deze query formulieren wordt de analyse functie die is opgegeven voor het veld genegeerd.

> [!NOTE]
> Wanneer een gedeeltelijke query reeks tekens bevat, zoals slashes in een URL-fragment, moet u mogelijk escape tekens toevoegen. In JSON wordt een slash `/` met een back slash met een escape-teken `\` . Als zodanig `search=/.*microsoft.com\/azure\/.*/` is de syntaxis voor het URL-fragment "Microsoft.com/azure/".

## <a name="solving-partialpattern-search-problems"></a>Problemen met zoeken in een deel/patroon oplossen

Als u wilt zoeken naar fragmenten of patronen of speciale tekens, kunt u de standaard-Analyzer vervangen door een aangepaste analyse functie die onder eenvoudigere token regels wordt uitgevoerd, waarbij de hele teken reeks in de index wordt bewaard. Als u een stap terug neemt, ziet de benadering er als volgt uit:

1. Een veld definiëren voor het opslaan van een onbeschadigde versie van de teken reeks (ervan uitgaande dat u de query tijd wilt analyseren en niet-analyseerde tekst)
1. Evalueer en kies uit de verschillende analyse functies die tokens op het juiste niveau van granulariteit genereren
1. De analyse functie toewijzen aan het veld
1. De index maken en testen

> [!TIP]
> Het evalueren van analyse functies is een iteratief proces dat veelvuldig opnieuw opgebouwde index vereist. U kunt deze stap eenvoudiger maken met behulp van Postman, de REST-Api's voor het maken van een [index](/rest/api/searchservice/create-index), het verwijderen van een [index](/rest/api/searchservice/delete-index), het[laden van documenten](/rest/api/searchservice/addupdate-or-delete-documents)en het zoeken van [documenten](/rest/api/searchservice/search-documents). Voor het laden van documenten moet de hoofd tekst van de aanvraag een kleine representatieve gegevensset bevatten die u wilt testen (bijvoorbeeld een veld met telefoon nummers of product codes). Met deze Api's in dezelfde postman-verzameling kunt u deze stappen snel door lopen.

## <a name="1---create-a-dedicated-field"></a>1-een speciaal veld maken

Analyseerers bepalen hoe termen worden getokend in een index. Omdat analyse functies per veld worden toegewezen, kunt u velden in uw index maken om te optimaliseren voor verschillende scenario's. U kunt bijvoorbeeld ' featureCode ' en ' featureCodeRegex ' definiëren voor het ondersteunen van normale Zoek opdrachten in volledige tekst op het eerste en geavanceerde patroon dat op het tweede punt overeenkomt. De analyse functies toegewezen aan elk veld bepalen hoe de inhoud van elk veld wordt getokend in de index.  

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

<a name="set-an-analyzer"></a>

## <a name="2---set-an-analyzer"></a>2: een analyse functie instellen

Bij het kiezen van een analyse functie die volledige-termijn tokens produceert, zijn de volgende analyse functies algemene keuzes:

| Analyse | Mogelijkheden |
|----------|-----------|
| [taal analyse](index-add-language-analyzers.md) | Hiermee behoudt u afbreek streepjes in samengestelde woorden of teken reeksen, klinker mutaties en verbale vormen. Als de query patronen streepjes bevatten, kan het gebruik van een taal analyse voldoende zijn. |
| [Zoek](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | De inhoud van het hele veld wordt als één periode getokend. |
| [spaties](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Scheidt alleen op spaties. Voor waarden die streepjes of andere tekens bevatten, worden beschouwd als één token. |
| [aangepaste analyse functie](index-add-custom-analyzers.md) | aanbevelingen Als u een aangepaste analyse functie maakt, kunt u zowel het tokenizer als het token filter opgeven. De vorige analyse functies moeten worden gebruikt als-is. Met een aangepaste analyse kunt u kiezen welke tokenizers-en Token filters u wilt gebruiken. <br><br>Een aanbevolen combi natie is het [tref woord tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) met een [token filter met een lagere Case](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). De ingebouwde [trefwoord analyse](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) biedt geen kleine letters in hoofd letters, wat kan leiden tot het mislukken van query's. Een aangepaste analyse functie biedt u een mechanisme voor het toevoegen van het filter voor het laagste Case-token. |

Als u een web-API-test programma zoals postman gebruikt, kunt u de [rest-aanroep van Test Analyzer](/rest/api/searchservice/test-analyzer) toevoegen om de getokende uitvoer te controleren.

U moet een gevulde index hebben om te kunnen werken met. Als u een bestaande index en een veld met streepjes of gedeeltelijke voor waarden hebt opgegeven, kunt u verschillende analyse functies op basis van specifieke termen uitproberen om te zien welke tokens worden verzonden.  

1. Controleer eerst de standaard analyse om te zien hoe de termen standaard worden getokend.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Evalueer het antwoord om te zien hoe de tekst binnen de index wordt getokend. U ziet dat elke term kleine letters, afbreek streepjes en subtekenreeksen bevat die zijn opgesplitst in afzonderlijke tokens. Alleen de query's die met deze tokens overeenkomen, retour neren dit document in de resultaten. Een query die "10 of" bevat, mislukt.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Wijzig nu de aanvraag om de `whitespace` or Analyzer te gebruiken `keyword` :

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Het antwoord bestaat nu uit één token, hoofd letters, met streepjes die als onderdeel van de teken reeks zijn bewaard. Als u wilt zoeken op een patroon of een gedeeltelijke term zoals "10-en", heeft de query-engine nu de basis voor het zoeken naar een overeenkomst.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Houd er rekening mee dat query parsers vaak kleine letters in een zoek expressie bij het samen stellen van de query-structuur. Als u een analyse functie gebruikt die tijdens het indexeren geen kleine letters voor tekst invoer bevat en u niet de verwachte resultaten krijgt, kan dit de reden zijn. De oplossing is het toevoegen van een token filter met een lagere case, zoals beschreven in de sectie ' aangepaste analyse functies gebruiken ' hieronder.

## <a name="3---configure-an-analyzer"></a>3-een analyse functie configureren
 
Of u analyse functies wilt evalueren of vooruit wilt gaan met een specifieke configuratie, u moet de analyse op de veld definitie opgeven en mogelijk de Analyzer zelf configureren als u geen gebruik maakt van een ingebouwde analyse functie. Bij het wisselen van analyse functies moet u doorgaans de index opnieuw samen stellen (verwijderen, opnieuw maken en opnieuw laden). 

### <a name="use-built-in-analyzers"></a>Ingebouwde analyse functies gebruiken

Ingebouwde analyse functies kunnen worden opgegeven met de naam van een `analyzer` eigenschap van een veld definitie, zonder dat er aanvullende configuratie is vereist in de index. In het volgende voor beeld ziet u hoe u het `whitespace` analyseprogramma op een veld kunt instellen. 

Zie voor andere scenario's [ingebouwde analyse](./index-add-custom-analyzers.md#built-in-analyzers)functies voor meer informatie over andere ingebouwde analyse functies. 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>Aangepaste analyse functies gebruiken

Als u een [aangepaste Analyzer](index-add-custom-analyzers.md)gebruikt, definieert u deze in de index met een door de gebruiker gedefinieerde combi natie van tokenizer, token filter, met mogelijke configuratie-instellingen. Vervolgens verwijzen we naar de definitie van een veld, net zoals u een ingebouwde Analyzer zou hebben.

Wanneer het doel van de volledige-termijn tokening is, wordt een aangepaste analyse functie aanbevolen die bestaat uit een **sleutel woord tokenizer** en een filter voor een **laag hoofdletter gebruik** .

+ Het tref woord tokenizer maakt één token voor de volledige inhoud van een veld.
+ Met het token filter met kleine letters worden hoofd letters getransformeerd in tekst met een kleine letter. Query-parsers bevatten doorgaans kleine letters tekst invoer. Met een lagere behuizing homogenizes u de invoer met de tokens-termen.

In het volgende voor beeld ziet u een aangepaste analyse functie die het tref woord tokenizer en een token filter voor kleine letters bevat.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> Het `keyword_v2` tokenizer `lowercase` -en Token filter zijn bekend bij het systeem en met behulp van de standaard configuraties. Daarom kunt u ernaar verwijzen met de naam zonder dat u ze eerst hoeft te definiëren.

## <a name="4---build-and-test"></a>4-maken en testen

Zodra u een index hebt gedefinieerd met analyse functies en veld definities die uw scenario ondersteunen, laadt u documenten met representatieve teken reeksen zodat u gedeeltelijke teken reeks query's kunt testen. 

In de vorige secties is de logica uitgelegd. In deze sectie wordt elke API beschreven die u moet aanroepen bij het testen van uw oplossing. Zoals eerder is vermeld, kunt u deze taken snel door lopen als u een interactief hulp programma voor het testen van een web gebruikt, zoals een bericht.

+ [Delete index](/rest/api/searchservice/delete-index) verwijdert een bestaande index met dezelfde naam, zodat u deze opnieuw kunt maken.

+ Met [Create Index](/rest/api/searchservice/create-index) maakt u de index structuur in uw zoek service, met inbegrip van analyse definities en velden met een analyse specificatie.

+ Met [documenten laden](/rest/api/searchservice/addupdate-or-delete-documents) worden documenten geïmporteerd die dezelfde structuur hebben als uw index, evenals Doorzoek bare inhoud. Na deze stap is de index gereed om query's uit te proberen of te testen.

+ [Test Analyzer](/rest/api/searchservice/test-analyzer) is geïntroduceerd in [set a Analyzer](#set-an-analyzer). Test enkele van de teken reeksen in uw index met behulp van verschillende analyse functies om te begrijpen hoe de termen worden getokend.

+ In [documenten zoeken](/rest/api/searchservice/search-documents) wordt uitgelegd hoe u een query aanvraag opbouwt met behulp van [eenvoudige syntaxis](query-simple-syntax.md) of de [volledige lucene-syntaxis](query-lucene-syntax.md) voor joker tekens en reguliere expressies.

  Voor gedeeltelijke term query's, zoals het opvragen van ' 3-6214 ' om een overeenkomst op ' + 1 (425) 703-6214 ' te vinden, kunt u de eenvoudige syntaxis gebruiken: `search=3-6214&queryType=simple` .

  Voor infix-en achtervoegsel query's, zoals het opvragen van ' num ' of ' numeriek ' om een overeenkomst te vinden op ' alfanumeriek ', gebruikt u de syntaxis Full lucene en een reguliere expressie: `search=/.*num.*/&queryType=full`

## <a name="tune-query-performance"></a>Queryprestaties afstemmen

Als u de aanbevolen configuratie implementeert die het keyword_v2 tokenizer-en het kleine-case-token filter bevat, ziet u mogelijk een afname in de query prestaties vanwege de extra token filter verwerking voor bestaande tokens in uw index. 

In het volgende voor beeld wordt een [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) toegevoegd om het voor voegsel sneller te laten overeenkomen. Er worden extra tokens gegenereerd voor de 2-25-teken combinaties die tekens bevatten: (niet alleen MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/VK, MSFT/SQL). 

Zoals u kunt Voorst Ellen, resulteert de extra tokening in een grotere index. Als u voldoende capaciteit hebt voor de grotere index, kan deze benadering met de snellere reactie tijd een betere oplossing zijn.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe analyse functies beide bijdragen om problemen op te vragen en query problemen op te lossen. Ga als volgt te werk om de analyse-impact op indexering en query verwerking nader te bekijken. In het bijzonder kunt u overwegen de analyse tekst-API te gebruiken voor het retour neren van een tokenve uitvoer zodat u precies ziet wat een Analyzer is voor uw index.

+ [Taalanalyse](search-language-support.md)
+ [Analyse functies voor tekst verwerking in azure Cognitive Search](search-analyzers.md)
+ [Tekst-API analyseren (REST)](/rest/api/searchservice/test-analyzer)
+ [Hoe zoeken in volledige tekst werkt (query architectuur)](search-lucene-query-architecture.md)