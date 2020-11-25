---
title: Analyse functies voor taal kundige en tekst verwerking
titleSuffix: Azure Cognitive Search
description: U kunt analyse functies toewijzen aan Doorzoek bare tekst velden in een index om de standaard standaard-lucene te vervangen door aangepaste, vooraf gedefinieerde of taalspecifieke alternatieven.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 544509a8c90c9273b748591509b1fa86510d71c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013816"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analyse functies voor tekst verwerking in azure Cognitive Search

Een *analyse* maakt deel uit van de [engine voor zoekopdrachten in volledige tekst](search-lucene-query-architecture.md) en is verantwoordelijk voor het verwerken van tekst in queryreeksen en geïndexeerde documenten. Tekst verwerking (ook wel lexicale analyse genoemd) is Transformatieve en wijzigt een teken reeks via acties, zoals:

+ Verwijder niet-essentiële woorden (stopwords) en interpunctie
+ Zinsdelen en afgebroken woorden in onderdeel delen splitsen
+ Kleine letters in hoofd letters
+ Verminder het aantal woorden in primitieve hoofd formulieren voor efficiëntie van de opslag, zodat treffers kunnen worden gevonden ongeacht het aantal tien tallen

De analyse is van toepassing op `Edm.String` velden die zijn gemarkeerd als zoekbaar, waarmee Zoek opdrachten in volledige tekst worden aangeduid. Voor velden met deze configuratie treedt tijdens het indexeren een analyse op wanneer er tokens worden gemaakt en vervolgens tijdens het uitvoeren van de query wanneer query's worden geparseerd en de engine scant op overeenkomende tokens. Een overeenkomst wordt waarschijnlijk vaker weer gegeven wanneer dezelfde analyse functie wordt gebruikt voor indexering en query's, maar u kunt de Analyzer voor elke werk belasting afzonderlijk instellen, afhankelijk van uw vereisten.

Query typen die niet in volledige tekst zoeken, zoals reguliere expressie of fuzzy zoek opdracht, worden niet door de analyse fase aan de query zijde door lopen. In plaats daarvan verzendt de parser deze teken reeksen rechtstreeks naar de zoek machine, met behulp van het patroon dat u opgeeft als basis voor de overeenkomst. Deze query formulieren vereisen doorgaans tokens met hele teken reeksen om het gebruik van patronen te laten overeenkomen. Als u hele tokens wilt ophalen tijdens het indexeren, hebt u mogelijk [aangepaste analyse](index-add-custom-analyzers.md)functies nodig. Zie [Zoeken in volledige tekst in Azure Cognitive Search](search-lucene-query-architecture.md)voor meer informatie over wanneer en waarom query termen worden geanalyseerd.

Voor meer achtergrond informatie over lexicale analyse, luistert u naar de volgende video clip voor een korte uitleg.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]

## <a name="default-analyzer"></a>Standaard-Analyzer  

In azure Cognitive Search query's wordt een analyse programma automatisch aangeroepen voor alle teken reeks velden die als doorzoekbaar zijn gemarkeerd. 

Azure Cognitive Search maakt standaard gebruik van de [Apache Lucene Standard Analyzer (standaard-lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html), die tekst in elementen afbreekt volgens de regels voor [Unicode-tekst segmentatie](https://unicode.org/reports/tr29/) . Daarnaast worden alle tekens in de vorm van een kleine letters door de standaard-Analyzer geconverteerd. Zowel geïndexeerde documenten als zoek termen voeren de analyse uit tijdens het indexeren en verwerken van query's.  

U kunt de standaard waarde voor een veld per veld overschrijven. Alternatieve analyse functies kunnen een [taal analyse](index-add-language-analyzers.md) zijn voor linguïstische verwerking, een [aangepaste analyse functie](index-add-custom-analyzers.md)of een vooraf gedefinieerde analyse van de [lijst met beschik bare analyse](index-add-custom-analyzers.md#AnalyzerTable)functies.

## <a name="types-of-analyzers"></a>Typen analyse functies

In de volgende lijst wordt beschreven welke analyse functies beschikbaar zijn in azure Cognitive Search.

| Categorie | Beschrijving |
|----------|-------------|
| [Standard-lucene Analyzer](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Standaard. Er is geen specificatie of configuratie vereist. Deze General-Purpose Analyzer werkt goed voor veel talen en scenario's.|
| Vooraf gedefinieerde analyse functies | Wordt aangeboden als een voltooid product dat is bedoeld om te worden gebruikt als-is. <br/>Er zijn twee typen: speciaal en taal. Wat maakt het ' vooraf gedefinieerd ' dat u naar de naam verwijst, zonder configuratie of aanpassing. <br/><br/>[Gespecialiseerde analyse functies (taal-neutraal)](index-add-custom-analyzers.md#AnalyzerTable) worden gebruikt wanneer tekst invoer gespecialiseerde verwerking of minimale verwerking vereist. Vooraf gedefinieerde analyse functies zonder taal zijn **Asciifolding**, **tref woord**, **patroon**, **eenvoudig**, **Stop**, **spatie**.<br/><br/>[Taal analysen](index-add-language-analyzers.md) worden gebruikt wanneer u ondersteuning voor uitgebreide taal functionaliteit voor afzonderlijke talen nodig hebt. Azure Cognitive Search biedt ondersteuning voor taal analyses van 35 en 50 micro soft voor de verwerking van natuurlijke taal. |
|[Analysevoorzieningen aanpassen](/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Verwijst naar een door de gebruiker gedefinieerde configuratie van een combi natie van bestaande elementen, die bestaat uit één tokenizer (vereist) en optionele filters (char of Token).|

Een paar vooraf gedefinieerde analyse functies, zoals **patroon** of **Stop**, ondersteunen een beperkte set configuratie opties. Als u deze opties wilt instellen, maakt u effectief een aangepaste analyse functie die bestaat uit de vooraf gedefinieerde analyse functie en een van de alternatieve opties die worden beschreven in de [vooraf gedefinieerde analyse verwijzing](index-add-custom-analyzers.md#AnalyzerTable). Net als bij elke aangepaste configuratie geeft u de nieuwe configuratie op met een naam, zoals *myPatternAnalyzer* om deze te onderscheiden van de Lucene-patroon analyse.

## <a name="how-to-specify-analyzers"></a>Analyse functies opgeven

Het instellen van een Analyzer is optioneel. In het algemeen kunt u de standaard standaard-lucene Analyzer eerst gebruiken om te zien hoe deze werkt. Als query's niet de verwachte resultaten retour neren, is het vaak de juiste oplossing om over te scha kelen naar een andere analyse functie.

1. Wanneer u een definitie van een veld in de [index](/rest/api/searchservice/create-index)maakt, stelt u de eigenschap  **Analyzer** in op een van de volgende opties: een [vooraf gedefinieerde analysator](index-add-custom-analyzers.md#AnalyzerTable) `keyword` , zoals een [taal analyse](index-add-language-analyzers.md) `en.microsoft` , zoals of een aangepaste analyse functie (gedefinieerd in hetzelfde index schema).  
 
   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
   ```

   Als u een [taal analyse](index-add-language-analyzers.md)gebruikt, moet u de eigenschap **Analyzer** gebruiken om deze op te geven. De eigenschappen **searchAnalyzer** en **indexAnalyzer** ondersteunen geen taal analyse functies.

1. U kunt ook **indexAnalyzer** en **searchAnalyzer** instellen om de Analyzer voor elke werk belasting te variëren. Deze eigenschappen worden samen ingesteld en vervangen de eigenschap **Analyzer** , die Null moet zijn. U kunt verschillende analyse functies gebruiken om gegevens voor te bereiden en op te halen als een van deze activiteiten vereist dat een specifieke trans formatie niet nodig is voor de andere.

   ```json
     "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "indexAnalyzer": "keyword",
      "searchAnalyzer": "whitespace"
    },
   ```

1. Voor aangepaste analyse functies maakt u een vermelding in de sectie **[analyse functies]** van de index en wijst u uw aangepaste analyse programma toe aan de veld definitie op basis van een van de vorige twee stappen. Zie [index maken](/rest/api/searchservice/create-index) en [aangepaste analyse functies toevoegen](index-add-custom-analyzers.md)voor meer informatie.

## <a name="when-to-add-analyzers"></a>Wanneer u analyse functies wilt toevoegen

De beste manier om analyseers toe te voegen en toe te wijzen, is tijdens de actieve ontwikkeling, wanneer het verwijderen en opnieuw maken van indexen de routine is.

Omdat analyse functies worden gebruikt voor Tokenize-voor waarden, moet u een analyse functie toewijzen wanneer het veld wordt gemaakt. Het toewijzen van **analyse** -of **indexAnalyzer** aan een veld dat al fysiek is gemaakt is niet toegestaan (hoewel u de eigenschap **searchAnalyzer** echter op elk gewenst moment kunt wijzigen zonder dat dit van invloed is op de index).

Als u de Analysator van een bestaand veld wilt wijzigen, moet u [de index volledig opnieuw samen stellen](search-howto-reindex.md) (u kunt geen afzonderlijke velden opnieuw samen stellen). Voor indexen in productie kunt u een nieuwe build uitstellen door een nieuw veld te maken met de nieuwe analyse toewijzing en deze te gebruiken in plaats van de oude. Gebruik [update-index](/rest/api/searchservice/update-index) om het nieuwe veld en [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) op te nemen. Later kunt u als onderdeel van de geplande index onderhoud de index opschonen om verouderde velden te verwijderen.

Als u een nieuw veld wilt toevoegen aan een bestaande index, roept u [update index](/rest/api/searchservice/update-index) aan om het veld toe te voegen en [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) te vullen.

Als u een aangepaste analyse functie wilt toevoegen aan een bestaande index, geeft u de **allowIndexDowntime** -vlag door in [update index](/rest/api/searchservice/update-index) als u deze fout wilt voor komen:

*De index update is niet toegestaan omdat deze downtime zou veroorzaken. Als u nieuwe analyse functies, tokenizers, token filters of teken filters wilt toevoegen aan een bestaande index, stelt u de query parameter ' allowIndexDowntime ' in op ' True ' in de aanvraag voor het bijwerken van de index. Houd er rekening mee dat met deze bewerking de index ten minste enkele seconden offline wordt gezet, waardoor uw indexerings-en query aanvragen mislukken. De prestaties en schrijf Beschik baarheid van de index kunnen enkele minuten na de index worden bijgewerkt, of langer voor zeer grote indexen. "*

## <a name="recommendations-for-working-with-analyzers"></a>Aanbevelingen voor het werken met analyse functies

In deze sectie vindt u advies over het werken met analyse functies.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Eén analyse functie voor lezen/schrijven, tenzij u specifieke vereisten hebt

Met Azure Cognitive Search kunt u verschillende analyse functies opgeven voor indexering en zoek acties via aanvullende veld eigenschappen **indexAnalyzer** en **searchAnalyzer** . Als u geen waarde opgeeft, wordt de analyseset met de eigenschap **Analyzer** gebruikt voor het indexeren en zoeken. Als **Analyzer** niet is opgegeven, wordt de standaard standaard-lucene Analyzer gebruikt.

Een algemene regel is het gebruik van dezelfde analyse functie voor indexering en query's, tenzij specifieke vereisten anders worden bepaald. Zorg ervoor dat u zorgvuldig test. Wanneer tekst verwerking afwijkt van de zoek-en indexerings tijd, loopt u het risico dat de query termen en geïndexeerde voor waarden niet overeenkomen wanneer de zoek-en indexerings analyse configuraties niet zijn uitgelijnd.

### <a name="test-during-active-development"></a>Testen tijdens actieve ontwikkeling

Voor het overschrijven van de Standard Analyzer is het opnieuw samen stellen van de index vereist. U kunt, indien mogelijk, bepalen welke analyse functies moeten worden gebruikt tijdens de actieve ontwikkeling voordat u een index naar productie rolt.

### <a name="inspect-tokenized-terms"></a>Token-termen controleren

Als voor een zoek opdracht verwachte resultaten niet worden geretourneerd, zijn de meest waarschijnlijke token verschillen tussen de invoer van de term en de tokens in de index. Als de tokens niet hetzelfde zijn, komen overeenkomsten niet overeen met realiseren. Als u tokenizer-uitvoer wilt controleren, kunt u het beste de [analyse-API](/rest/api/searchservice/test-analyzer) als een hulp programma voor onderzoek gebruiken. Het antwoord bestaat uit tokens, zoals wordt gegenereerd door een specifieke analyse functie.

<a name="examples"></a>

## <a name="rest-examples"></a>REST-voor beelden

In de onderstaande voor beelden worden de analyse definities voor enkele van de belangrijkste scenario's weer gegeven.

+ [Voor beeld van aangepaste analyse](#Custom-analyzer-example)
+ [Analyse functies toewijzen aan een voor beeld van een veld](#Per-field-analyzer-assignment-example)
+ [Analyse functies combi neren voor indexering en zoek acties](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Language Analyzer-voor beeld](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Voor beeld van aangepaste analyse

In dit voor beeld ziet u een analyse definitie met aangepaste opties. Aangepaste opties voor teken filters, tokenizers en Token filters worden afzonderlijk opgegeven als benoemde constructs en vervolgens verwezen in de analyse definitie. Vooraf gedefinieerde elementen worden gebruikt als-is en worden simpelweg naar de naam verwezen.

Dit voor beeld door lopen:

* Analyse functies zijn een eigenschap van de veld klasse voor een doorzoekbaar veld.
* Een aangepaste analyse functie maakt deel uit van een index definitie. Het kan licht worden aangepast (u kunt bijvoorbeeld één optie in één filter aanpassen) of op meerdere locaties aangepast.
* In dit geval is de aangepaste Analyzer "my_analyzer", die op zijn beurt gebruikmaakt van een aangepaste standaard tokenizer "my_standard_tokenizer" en twee token filters: kleine en aangepaste asciifolding-filter "my_asciifolding".
* Ook worden er 2 aangepaste teken filters map_dash en remove_whitespace gedefinieerd. De eerste Hiermee vervangt u alle streepjes door onderstrepings tekens, terwijl de tweede een spatie verwijdert. Spaties moeten UTF-8-code ring zijn in de toewijzings regels. De teken filters worden vóór het tokenen toegepast en beïnvloeden de resulterende tokens (de standaard-tokenizer pauzes op een streepje en spaties, maar niet op een onderstrepings teken).

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Voor beeld van analyse toewijzing per veld

Standaard-Analyzer is de standaard instelling. Stel dat u de standaard instelling wilt vervangen door een andere vooraf gedefinieerde analyse, zoals de patroon analyse. Als u geen aangepaste opties instelt, hoeft u deze alleen op naam op te geven in de veld definitie.

Het element ' Analyzer ' overschrijft de standaard analyse functie op basis van een veld. Er is geen globale onderdrukking. In dit voor beeld `text1` maakt gebruik van de patroon analyse en `text2` , waarmee geen Analyzer wordt opgegeven, gebruikt de standaard waarde.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
```

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Analyse functies combi neren voor indexerings-en zoek bewerkingen

De Api's bevatten aanvullende index kenmerken voor het opgeven van verschillende analyse functies voor indexering en zoek opdrachten. De kenmerken **searchAnalyzer** en **indexAnalyzer** moeten worden opgegeven als een paar, waarbij het één **analyse** kenmerk wordt vervangen.


```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
```

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Language Analyzer-voor beeld

Velden met teken reeksen in verschillende talen kunnen gebruikmaken van een taal analyse, terwijl andere velden de standaard waarde behouden (of een andere vooraf gedefinieerde of aangepaste analyse functie gebruiken). Als u een taal analyse gebruikt, moet deze worden gebruikt voor indexerings-en zoek bewerkingen. Velden die gebruikmaken van een taal analyse kunnen geen verschillende analyse functies hebben voor indexering en zoek opdrachten.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
```

## <a name="c-examples"></a>C#-voor beelden

Als u de .NET SDK-code voorbeelden gebruikt, kunt u deze voor beelden toevoegen om analyse functies te gebruiken of te configureren.

+ [Een ingebouwde analyse functie toewijzen](#Assign-a-language-analyzer)
+ [Een analyse functie configureren](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Een taal analyse toewijzen

Elke Analyzer die wordt gebruikt als-is, zonder configuratie, is opgegeven voor een veld definitie. Er is geen vereiste voor het maken van een vermelding in de sectie **[analyse functies]** van de index. 

In dit voor beeld worden de Engelse en Franse analyse functies van micro soft toegewezen aan de velden Beschrijving. Het is een fragment dat is gemaakt op basis van een grotere definitie van de hotels-index, gemaakt met behulp van de klasse hotel in het hotels.cs-bestand van het [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) -voor beeld.

Roep [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer)aan, geef het type [LexicalAnalyzerName](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) op dat een tekst analyse biedt die wordt ondersteund in azure Cognitive Search.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Een aangepaste analyse functie definiëren

Wanneer aanpassing of configuratie vereist is, moet u een Analyzer-constructie toevoegen aan een index. Zodra u het hebt gedefinieerd, kunt u de definitie van het veld toevoegen, zoals wordt getoond in het vorige voor beeld.

Maak een [CustomAnalyzer](/dotnet/api/azure.search.documents.indexes.models.customanalyzer) -object. Zie [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs)voor meer voor beelden.

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Volgende stappen

+ Bekijk onze uitgebreide uitleg [over de manier waarop zoeken in volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md). In dit artikel worden voor beelden gebruikt om gedrag te verklaren dat op het Opper vlak kan lijken op het licht.

+ Probeer extra query syntaxis uit de sectie voor beeld van [Zoeken in documenten](/rest/api/searchservice/search-documents#bkmk_examples) of van een [eenvoudige query syntaxis](query-simple-syntax.md) in Search Explorer in de portal.

+ Meer informatie over het Toep assen van [taalspecifieke lexicale analyse](index-add-language-analyzers.md)functies.

+ [Aangepaste analyse functies configureren](index-add-custom-analyzers.md) voor een minimale verwerking of gespecialiseerde verwerking op afzonderlijke velden.

## <a name="see-also"></a>Zie ook

 [REST API voor documenten zoeken](/rest/api/searchservice/search-documents) 

 [Eenvoudige query syntaxis](query-simple-syntax.md) 

 [Volledige lucene-query syntaxis](query-lucene-syntax.md) 
 
 [Zoekresultaten verwerken](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png