---
title: Aangepaste analyse functies toevoegen aan teken reeks velden
titleSuffix: Azure Cognitive Search
description: Configureer tekst-tokenizers en-teken filters voor het uitvoeren van tekst analyse op teken reeksen tijdens het indexeren en query's.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 831e57a68c79c245b96baec0fc3d062c4c9112c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604437"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Aangepaste analyse functies toevoegen aan teken reeks velden in een Azure Cognitive Search-index

Een *aangepaste analyse functie* is een combi natie van tokenizer, een of meer token filters en een of meer teken filters die u in de zoek index definieert, en vervolgens naar veld definities waarvoor aangepaste analyse is vereist. De Tokenizer is verantwoordelijk voor het afbreken van tekst in tokens en de token filters voor het wijzigen van tokens die zijn gegenereerd door de tokenizer. Met teken filters wordt de invoer tekst voor bereid voordat deze wordt verwerkt door de tokenizer. 

Met een aangepaste analyse functie kunt u het proces voor het converteren van tekst in Indexeer bare en Doorzoek bare tokens controleren, zodat u kunt kiezen welke typen analyse of filter u wilt aanroepen en in welke volg orde deze zich voordoen. Als u een ingebouwde analyse met aangepaste opties wilt gebruiken, zoals het wijzigen van de maxTokenLength in Standard, maakt u een aangepaste analyse functie met een door de gebruiker gedefinieerde naam om deze opties in te stellen.

In situaties waarin aangepaste analyse functies handig kunnen zijn, kunt u het volgende doen:

- Teken filters gebruiken om HTML-opmaak te verwijderen voordat tekst invoer wordt getokend of door bepaalde tekens of symbolen te vervangen.

- Fonetisch zoeken. Voeg een fonetisch filter toe om te zoeken op basis van de manier waarop een woord klinkt, niet hoe het wordt gespeld.  

- Lexicale analyse uitschakelen. Gebruik de trefwoord analyse om Doorzoek bare velden te maken die niet worden geanalyseerd.  

- Snel voor voegsel/achtervoegsel zoeken. Voeg het rand-N-gram-token filter toe om voor voegsels van woorden te indexeren, zodat er snel voorvoegsels kunnen worden gevonden. Combi neer deze met het filter voor omgekeerde tokens om achtervoegsels te vergelijken.  

- Aangepaste tokening. Gebruik bijvoorbeeld het spatie tokenizer om zinnen te kraken in tokens met behulp van een spatie als scheidings teken  

- ASCII-vouwen. Voeg het standaard filter voor ASCII-vouwing toe om diakritische tekens zoals ö of ê in zoek termen te normaliseren.  

Als u een aangepaste analyse functie wilt maken, geeft u deze op in de sectie ' analyse functies ' van een index tijdens de ontwerp fase en verwijst u deze naar de velden Doorzoek bare, EDM. String met behulp van de eigenschap ' Analyzer ' of het ' indexAnalyzer ' en het ' searchAnalyzer-paar.

> [!NOTE]  
> Aangepaste analyse functies die u maakt, worden niet weer gegeven in de Azure Portal. De enige manier om een aangepaste Analyzer toe te voegen, is door middel van code die een index definieert. 

## <a name="create-a-custom-analyzer"></a>Een aangepaste analyse maken

Een analyse definitie bevat een naam, type, een of meer teken filters, Maxi maal één tokenizer en een of meer token filters voor het verwerken van post-tokening. Teken filters worden toegepast vóór tokenisatie. Token filters en teken filters worden van links naar rechts toegepast.

- Namen in een aangepaste analyse functie moeten uniek zijn en kunnen niet hetzelfde zijn als een van de ingebouwde analyse functies, tokenizers, token filters of teken filters. Het mag alleen letters, cijfers, spaties, streepjes of onderstrepings tekens bevatten, mag alleen beginnen en eindigen met een alfanumeriek teken en mag niet langer zijn dan 128 tekens. 

- Het type moet #Microsoft. Azure. Search. CustomAnalyzer.

- ' charFilters ' kan een of meer filters zijn van [teken filters](#CharFilter), verwerkt vóór tokenisatie, in de aangegeven volg orde. Sommige teken filters hebben opties, die kunnen worden ingesteld in een sectie ' charFilter '. Teken filters zijn optioneel.

- ' tokenizer ' is precies één [tokenizer](#tokenizers). Een waarde is vereist. Als u meer dan één tokenizer nodig hebt, kunt u meerdere aangepaste analyse functies maken en deze toewijzen aan een veld per veld in uw index schema.

- ' tokenFilters ' kan een of meer filters zijn van [token filters](#TokenFilters), verwerkt na de tokens in de aangegeven volg orde. Voor token filters met opties voegt u een sectie ' tokenFilter ' toe om de configuratie op te geven. Token filters zijn optioneel.

Analyse functies mogen geen tokens produceren die langer zijn dan 300 tekens, anders mislukt de indexering. Als u een lang token wilt knippen of wilt uitsluiten, gebruikt u respectievelijk de **TruncateTokenFilter** en de **LengthTokenFilter** . Zie [**token filters**](#TokenFilters) voor verwijzing.

```json
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Binnen een index definitie kunt u deze sectie ergens in de hoofd tekst van een aanvraag voor het maken van een index plaatsen, maar doorgaans wordt deze aan het einde geplaatst:  

```json
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

De analyse definitie maakt deel uit van de grotere index. Definities voor teken filters, tokenizers en Token filters worden alleen toegevoegd aan de index als u aangepaste opties instelt. Als u een bestaand filter of tokenizer wilt gebruiken, geeft u dit op naam op in de analyse definitie. Zie [Create Index (rest) (Engelstalig)](/rest/api/searchservice/create-index)voor meer informatie. Zie voor meer voor beelden [analyse functies toevoegen in Azure Cognitive Search](search-analyzers.md#examples).

## <a name="test-custom-analyzers"></a>Aangepaste analyse functies testen

U kunt de [Test Analyzer (rest)](/rest/api/searchservice/test-analyzer) gebruiken om te zien hoe een Analyzer de opgegeven tekst in tokens afbreekt.

**Aanvraag**

```http
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```

**Response**

```http
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Aangepaste analyse functies bijwerken

Zodra een analyse programma, een tokenizer, een token filter of een teken filter is gedefinieerd, kan het niet worden gewijzigd. Nieuwe kunnen alleen worden toegevoegd aan een bestaande index als de `allowIndexDowntime` vlag is ingesteld op True in de aanvraag voor het bijwerken van de index:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Met deze bewerking wordt uw index ten minste enkele seconden offline gezet, waardoor uw indexerings-en query aanvragen mislukken. De prestaties en schrijf Beschik baarheid van de index kunnen enkele minuten duren nadat de index is bijgewerkt, of langer voor zeer grote indexen, maar deze effecten zijn tijdelijk en worden op een later moment opgelost.

<a name="built-in-analyzers"></a>

## <a name="built-in-analyzers"></a>Ingebouwde analyse functies

Als u een ingebouwde Analyzer met aangepaste opties wilt gebruiken, is het maken van een aangepaste Analyzer het mechanisme waarmee u deze opties opgeeft. Als u daarentegen een ingebouwde analyse functie wilt gebruiken, hoeft u alleen maar [naar de naam te verwijzen](search-analyzers.md#how-to-specify-analyzers) in de veld definitie.

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Beschrijving en opties**|  
|-----------------|-------------------------------|---------------------------|  
|[Zoek](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (type is alleen van toepassing wanneer opties beschikbaar zijn) |Behandelt de gehele inhoud van een veld als één token. Dit is handig voor gegevens zoals post codes, Id's en sommige product namen.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Flexibele tekst in termen worden gescheiden door middel van een reguliere-expressie patroon. </br></br>**Opties** </br></br>kleine letters (type: BOOL): bepaalt of voor waarden in kleine letters worden getypt. De standaard waarde is True. </br></br>[patroon](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (type: teken reeks): een reguliere-expressie patroon dat overeenkomt met token scheidings tekens. De standaard waarde is `\W+` , die overeenkomt met niet-Word-tekens. </br></br>[Flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (type: String): reguliere expressie vlaggen. De standaard waarde is een lege teken reeks. Toegestane waarden: CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, multiline, UNICODE_CASE, UNIX_LINES </br></br>stopwords (type: teken reeks matrix): een lijst met stopwords. De standaard waarde is een lege lijst.|  
|[Simple](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn) |Hiermee wordt tekst op niet-letters verdeeld en omgezet in kleine letters. |  
|[standaard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) </br>(Ook wel Standard. lucene genoemd)|StandardAnalyzer|Standaard-lucene Analyzer, samengesteld uit het standaard tokenizer, het kleine filter en het filter stoppen. </br></br>**Opties** </br></br>maxTokenLength (type: int): de maximale token lengte. De standaard waarde is 255. Tokens die langer zijn dan de maximale lengte, worden gesplitst. De maximale token lengte die kan worden gebruikt, is 300 tekens. </br></br>stopwords (type: teken reeks matrix): een lijst met stopwords. De standaard waarde is een lege lijst.|  
|standardasciifolding. lucene|(type is alleen van toepassing wanneer opties beschikbaar zijn) |Standard Analyzer met ASCII-vouw filter. |  
|[tab](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Hiermee wordt tekst op niet-letters verdeeld, worden de token filters voor kleine en stop woord toegepast. </br></br>**Opties** </br></br>stopwords (type: teken reeks matrix): een lijst met stopwords. De standaard waarde is een vooraf gedefinieerde lijst voor Engels. |  
|[spaties](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn) |Een analyse die gebruikmaakt van de spatie tokenizer. Tokens die langer zijn dan 255 tekens worden gesplitst.|  

 <sup>1</sup> analyse typen worden altijd voorafgegaan door de code ' #Microsoft. Azure. Search ', zodat ' PatternAnalyzer ' daad werkelijk als ' #Microsoft. Azure. Search. PatternAnalyzer ' zou worden opgegeven. Het voor voegsel voor de boog is verwijderd, maar het voor voegsel is vereist in uw code.

De analyzer_type wordt alleen gegeven voor analyse functies die kunnen worden aangepast. Als er geen opties zijn, zoals in het geval van de trefwoord analyse, zijn er geen gekoppelde #Microsoft. Azure. search-type.

<a name="CharFilter"></a>

## <a name="character-filters"></a>Tekenfilters

In de onderstaande tabel zijn de teken filters die zijn geïmplementeerd met Apache Lucene gekoppeld aan de Lucene API-documentatie.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Beschrijving en opties**|  
|--------------------|---------------------------------|---------------------------|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Een char-filter dat een HTML-constructie probeert te verwijderen.|  
|[toewijzing](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Een char-filter waarmee toewijzingen worden toegepast die zijn gedefinieerd met de optie toewijzingen. Overeenkomende is Greedy (langste patroon dat overeenkomt met een bepaald punt in WINS). Vervanging mag de lege teken reeks zijn.  </br></br>**Opties**  </br></br> toewijzingen (type: teken reeks matrix)-een lijst met toewijzingen van de volgende indeling: "a =>b" (alle exemplaren van het teken "a" worden vervangen door het teken "b"). Vereist.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Een char-filter waarmee tekens in de invoer teken reeks worden vervangen. Er wordt gebruikgemaakt van een reguliere expressie om te identificeren welke teken reeksen moeten worden bewaard en een vervangend patroon om te bepalen welke tekens moeten worden vervangen. Bijvoorbeeld invoer tekst = "AA BB AA", patroon = "(AA) \\ \s + (BB)" vervangen = "$ 1 # $2", resultaat = "AA # BB AA # BB".  </br></br>**Opties**  </br></br>patroon (type: teken reeks)-vereist.  </br></br>vervanging (type: teken reeks)-vereist.|  

 <sup>1</sup> char-filter typen worden altijd voorafgegaan door de code ' #Microsoft. Azure. Search ', zodat ' MappingCharFilter ' werkelijk wordt opgegeven als ' #Microsoft. Azure. Search. MappingCharFilter. We hebben het voor voegsel verwijderd om de breedte van de tabel te verminderen, maar vergeet niet om deze op te nemen in uw code. U ziet dat char_filter_type alleen wordt gegeven voor filters die kunnen worden aangepast. Als er geen opties zijn, zoals in het geval van html_strip, is er geen gekoppelde #Microsoft. Azure. search-type.

<a name="tokenizers"></a>

## <a name="tokenizers"></a>Tokenizers

Een tokenizer verdeelt doorlopende tekst in een reeks tokens, zoals het breken van een zin in woorden. In de onderstaande tabel zijn de tokenizers die zijn geïmplementeerd met Apache Lucene gekoppeld aan de Lucene API-documentatie.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Beschrijving en opties**|  
|------------------|-------------------------------|---------------------------|  
|[klassiek](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Tokenizer op basis van grammatica die geschikt is voor het verwerken van de meeste documenten in de Europese taal.  </br></br>**Opties**  </br></br>maxTokenLength (type: int): de maximale token lengte. Standaard: 255, maximum: 300. Tokens die langer zijn dan de maximale lengte, worden gesplitst.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|De invoer van een rand Tokenizes in n-g van gegeven grootte (s).  </br></br> **Opties**  </br></br>minGram (type: int)-standaard: 1, maximum: 300.  </br></br>maxGram (type: int)-standaard: 2, maximum: 300. Moet groter zijn dan minGram.  </br></br>tokenChars (type: teken reeks matrix): teken klassen die moeten worden bewaard in de tokens. Toegestane waarden: </br>"letter", "cijfer", "Whites", "interpunctie", "Symbol". De standaard waarde is een lege matrix. alle tekens worden bewaard. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Verzendt de volledige invoer als één token.  </br></br>**Opties**  </br></br>maxTokenLength (type: int): de maximale token lengte. Standaard: 256, maximum: 300. Tokens die langer zijn dan de maximale lengte, worden gesplitst.|  
|[letters](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Hiermee wordt tekst op niet-letters verdeeld. Tokens die langer zijn dan 255 tekens worden gesplitst.|  
|[kleine](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Hiermee wordt tekst op niet-letters verdeeld en omgezet in kleine letters. Tokens die langer zijn dan 255 tekens worden gesplitst.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Hiermee wordt tekst verdeeld aan de hand van taalspecifieke regels.  </br></br>**Opties**  </br></br>maxTokenLength (type: int): de maximale token lengte, standaard: 255, maximum: 300. Tokens die langer zijn dan de maximale lengte, worden gesplitst. Tokens die langer zijn dan 300 tekens worden eerst gesplitst in tokens met een lengte van 300, en elk van deze tokens wordt gesplitst op basis van de maxTokenLength-set.  </br></br>isSearchTokenizer (type: BOOL): Stel deze waarde in op True als de zoek tokenizer is ingesteld op False als de Indexing tokenizer wordt gebruikt. </br></br>taal (type: teken reeks): taal die moet worden gebruikt, standaard ' Engels '. Toegestane waarden zijn: </br>"Bengalees", "Bulgaars", "Catalaans ', ' chineseSimplified ', ' chineseTraditional ', ' Kroatisch ', ' Tsjechisch ', ' Deens ', ' Nederlands ', ' Nederlands ', ' Frans ', ' Duits ', ' Grieks ', ' Gujarati ', ' hindi ', ' IJslands ', ' Indonesisch ', ' Italiaans ', ' Japans ', ' Kannada ', ' Koreaans ', ' Maleis", "Malayalam", "Marathi", "norwegianBokmaal", "Pools", "Portugees", "portugueseBrazilian", "Punjabi", "Roemeens", "Russisch", "serbianCyrillic", ' serbianLatin ', ' Sloveens ', ' Spaans ', ' Zweeds ', ' Tamil ', ' Telugu ', ' Thais ', ' Oekraïens ', ' Urdu ', ' Vietnamees ' |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Verdeelt tekst met taalspecifieke regels en reduceert woorden naar hun basis formulieren </br></br>**Opties** </br></br>maxTokenLength (type: int): de maximale token lengte, standaard: 255, maximum: 300. Tokens die langer zijn dan de maximale lengte, worden gesplitst. Tokens die langer zijn dan 300 tekens worden eerst gesplitst in tokens met een lengte van 300, en elk van deze tokens wordt gesplitst op basis van de maxTokenLength-set. </br></br> isSearchTokenizer (type: BOOL): Stel deze waarde in op True als de zoek tokenizer is ingesteld op False als de Indexing tokenizer wordt gebruikt. </br></br>taal (type: teken reeks): taal die moet worden gebruikt, standaard ' Engels '. Toegestane waarden zijn: </br>"Arabisch", "Bengalees", "Bulgaars", "Catalaans", "Kroatisch ', ' Tsjechisch ', ' Deens ', ' Nederlands ', ' Engels ', ' Estlands ', ' Fins ', ' Frans ', ' Duits ', ' Grieks ', ' Gujarati ', ' Hebreeuws ', ' hindi ', ' Hong aars ', ' IJslands ', ' Indonesisch ', ' Italiaans ', ' Kannada ', ' Lets ', ' Litouws", "Maleis", "Malayalam", "Marathi", "norwegianBokmaal", "Pools", "Portugees", "portugueseBrazilian", "Punjabi", "Roemeens", "Russisch", "serbianCyrillic", "serbianLatin", "Slowaaks", "Sloveens", "Spaans", "Zweeds", "Tamil", "Telugu", "Turks", "Oekraïens", "Urdu" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenizes de invoer in n-g van de opgegeven grootte (n). </br></br>**Opties** </br></br>minGram (type: int)-standaard: 1, maximum: 300. </br></br>maxGram (type: int)-standaard: 2, maximum: 300. Moet groter zijn dan minGram. </br></br>tokenChars (type: teken reeks matrix): teken klassen die moeten worden bewaard in de tokens. Toegestane waarden: "letter", "cijfer", "Whites", "interpunctie", "Symbol". De standaard waarde is een lege matrix. alle tekens worden bewaard. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizer voor op paden lijkende hiërarchieën. **Opties** </br></br>scheidings teken (type: teken reeks)-standaard:/. </br></br>vervanging (type: teken reeks): als deze is ingesteld, wordt het scheidings teken vervangen. Standaard hetzelfde als de waarde van het scheidings teken. </br></br>maxTokenLength (type: int): de maximale token lengte. Standaard: 300, maximum: 300. Paden die langer zijn dan maxTokenLength, worden genegeerd. </br></br>omgekeerd (type: BOOL): als deze waarde True is, wordt token in omgekeerde volg orde gegenereerd. Standaard: onwaar. </br></br>overs Laan (type: BOOL)-initiële tokens die u wilt overs Laan. De standaardwaarde is 0.|  
|[pattern](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Deze tokenizer maakt gebruik van regex-patroon vergelijking om afzonderlijke tokens te maken. </br></br>**Opties** </br></br> [patroon](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (type: teken reeks): regulier expressie patroon dat overeenkomt met token scheidings tekens. De standaard waarde is `\W+` , die overeenkomt met niet-Word-tekens. </br></br>[Flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (type: String): reguliere expressie vlaggen. De standaard waarde is een lege teken reeks. Toegestane waarden: CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, multiline, UNICODE_CASE, UNIX_LINES </br></br>groep (type: int): welke groep u wilt extra heren in tokens. De standaard waarde is-1 (splitsen).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|De tekst die volgt op de regels van de [Unicode-tekst segmentatie](https://unicode.org/reports/tr29/)wordt afgebroken. </br></br>**Opties** </br></br>maxTokenLength (type: int): de maximale token lengte. Standaard: 255, maximum: 300. Tokens die langer zijn dan de maximale lengte, worden gesplitst.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes url's en e-mail berichten als één token. </br></br>**Opties** </br></br> maxTokenLength (type: int): de maximale token lengte. Standaard: 255, maximum: 300. Tokens die langer zijn dan de maximale lengte, worden gesplitst.|  
|[spaties](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn) |Hiermee wordt tekst op witruimte gesplitst. Tokens die langer zijn dan 255 tekens worden gesplitst.|  

 <sup>1</sup> tokenizer-typen worden altijd voorafgegaan door de code ' #Microsoft. Azure. Search ', zodat ' ClassicTokenizer ' daad werkelijk als ' #Microsoft. Azure. Search. ClassicTokenizer ' zou worden opgegeven. We hebben het voor voegsel verwijderd om de breedte van de tabel te verminderen, maar vergeet niet om deze op te nemen in uw code. U ziet dat tokenizer_type alleen wordt gegeven voor tokenizers die kunnen worden aangepast. Als er geen opties zijn, zoals in het geval van de letter tokenizer, is er geen gekoppelde #Microsoft. Azure. search-type.

<a name="TokenFilters"></a>

## <a name="token-filters"></a>Tokenfilters

Een token filter wordt gebruikt om de tokens die door een tokenizer zijn gegenereerd, te filteren of te wijzigen. U kunt bijvoorbeeld een kleine filter opgeven waarmee alle tekens worden omgezet in kleine letters. U kunt meerdere token filters hebben in een aangepaste analyse functie. Token filters worden uitgevoerd in de volg orde waarin ze worden weer gegeven. 

In de onderstaande tabel zijn de token filters die zijn geïmplementeerd met Apache Lucene gekoppeld aan de API-documentatie van Lucene.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Beschrijving en opties**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Een token filter dat de Arabische normalisatie voor het normaliseren van de orthography.|  
|[tekens](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Verwijdert alle tekens na een apostrof (inclusief de apostrof zelf). |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Converteert alfabetische, numerieke en symbolische Unicode-tekens die zich niet in de eerste 127 ASCII-tekens (het ' Basic Latijn ' Unicode-blok) in hun ASCII-equivalenten bevinden, als er een bestaat.<br /><br /> **Opties**<br /><br /> preserveOriginal (type: BOOL): als deze eigenschap True is, wordt het oorspronkelijke token bewaard. De standaardwaarde is false.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Formulieren bigrams van de CJK-termen die worden gegenereerd op basis van StandardTokenizer.<br /><br /> **Opties**<br /><br /> ignoreScripts (type: teken reeks matrix)-scripts die moeten worden genegeerd. Toegestane waarden zijn: "Han", "Hiragana", "Katakana", "Hangul". De standaard waarde is een lege lijst.<br /><br /> outputUnigrams (type: BOOL): Stel deze waarde in op True als u altijd zowel unigrams als bigrams wilt uitvoeren. De standaardwaarde is false.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Normaliseert CJK-breedte verschillen. Met worden de volledige breedte van ASCII-varianten uitgebreid naar de equivalente basis varianten van Latijns en halve breedte in de equivalente Kana. |  
|[klassiek](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Hiermee worden de Engelse possessives en punten van acroniemen verwijderd. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Maak bigrams voor frequent voorkomende voor waarden tijdens het indexeren. Er zijn nog steeds enkele termen geïndexeerd, met bigrams overlay.<br /><br /> **Opties**<br /><br /> commonWords (type: teken reeks matrix): de reeks veelgebruikte woorden. De standaard waarde is een lege lijst. Vereist.<br /><br /> ignoreCase (type: BOOL)-Indien waar, is het overeenkomende hoofdletter gevoelig. De standaardwaarde is false.<br /><br /> queryMode (type: BOOL): genereert bigrams vervolgens worden algemene woorden en enkele termen verwijderd, gevolgd door een gemeen schappelijk woord. De standaardwaarde is false.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Ontleedde samengestelde woorden in veel Germanic talen.<br /><br /> **Opties**<br /><br /> Woorden lijst (type: teken reeks matrix): de lijsten met woorden die moeten worden vergeleken. De standaard waarde is een lege lijst. Vereist.<br /><br /> minWordSize (type: int): alleen woorden die langer zijn dan wordt verwerkt. De standaard waarde is 5.<br /><br /> minSubwordSize (type: int): alleen subwoordjes die langer zijn dan dit worden gegenereerd. De standaard waarde is 2.<br /><br /> maxSubwordSize (type: int): alleen subwoordjes die korter zijn dan dit worden gegenereerd. De standaardwaarde is 15.<br /><br /> onlyLongestMatch (type: BOOL): Voeg alleen het langste overeenkomende subwoord toe om uit te voeren. De standaardwaarde is false.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Genereert n-g van de opgegeven grootte (n) vanaf de voor-of achterkant van een invoer token.<br /><br /> **Opties**<br /><br /> minGram (type: int)-standaard: 1, maximum: 300.<br /><br /> maxGram (type: int)-standaard: 2, maximum 300. Moet groter zijn dan minGram.<br /><br /> kant (type: teken reeks): geeft aan op welke zijde van de invoer het n-gram moet worden gegenereerd. Toegestane waarden: front, back |  
|[elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Hiermee verwijdert u elisions. Bijvoorbeeld ' L'Avion ' (het vlak) wordt geconverteerd naar ' Avion ' (vlieg tuig).<br /><br /> **Opties**<br /><br /> artikelen (type: teken reeks matrix): een set artikelen die u wilt verwijderen. De standaard waarde is een lege lijst. Als er geen lijst met artikelen is ingesteld, worden standaard alle Franse artikelen verwijderd.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Normaliseert Duitse tekens volgens de heuristiek van het [German2 Snowball-algoritme](https://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Normaliseert tekst in hindi om enkele verschillen in spelling variaties te verwijderen. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normaliseert de Unicode-weer gave van tekst in Indische talen.
|[werk](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Een token filter waarmee alleen tokens met tekst in de opgegeven lijst met woorden worden bewaard.<br /><br /> **Opties**<br /><br /> keepWords (type: teken reeks matrix): een lijst met woorden die moeten worden bewaard. De standaard waarde is een lege lijst. Vereist.<br /><br /> keepWordsCase (type: BOOL): als deze waarde True is, worden alle woorden eerst in kleine letters verkort. De standaardwaarde is false.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Hiermee worden termen als tref woorden gemarkeerd.<br /><br /> **Opties**<br /><br /> tref woorden (type: teken reeks matrix): een lijst met woorden die als tref woorden moeten worden gemarkeerd. De standaard waarde is een lege lijst. Vereist.<br /><br /> ignoreCase (type: BOOL): als deze waarde True is, worden alle woorden eerst in kleine letters verkort. De standaardwaarde is false.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Elk binnenkomend token wordt twee maal per keer verzonden als sleutel woord en eenmaal als niet-sleutel woord. |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Een kstem-filter met hoge prestaties voor Engels. |  
|[length](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Hiermee verwijdert u woorden die te lang of te kort zijn.<br /><br /> **Opties**<br /><br /> min (type: int): het minimum aantal. Standaard: 0, maximum: 300.<br /><br /> Max (type: int): het maximum aantal. Standaard: 300, maximum: 300.|  
|[limiet](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Micro soft. Azure. Search. LimitTokenFilter|Hiermee beperkt u het aantal tokens tijdens het indexeren.<br /><br /> **Opties**<br /><br /> maxTokenCount (type: int)-maximum aantal te produceren tokens. De standaardwaarde is 1.<br /><br /> consumeAllTokens (type: BOOL)-of alle tokens uit de invoer moeten worden verbruikt, zelfs als maxTokenCount is bereikt. De standaardwaarde is false.|  
|[kleine](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Normaliseert token tekst naar kleine letters. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Genereert een n-g van de opgegeven grootte (n).<br /><br /> **Opties**<br /><br /> minGram (type: int)-standaard: 1, maximum: 300.<br /><br /> maxGram (type: int)-standaard: 2, maximum 300. Moet groter zijn dan minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Maakt gebruik van Java regexs om meerdere tokens te verzenden, één voor elke opname groep in een of meer patronen.<br /><br /> **Opties**<br /><br /> patronen (type: teken reeks matrix): een lijst met patronen die moeten worden vergeleken met elk token. Vereist.<br /><br /> preserveOriginal (type: BOOL): Stel deze waarde in op True om het oorspronkelijke token te retour neren, zelfs als een van de patronen overeenkomt met de standaard waarde: True |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Een token filter waarmee een patroon wordt toegepast op elk token in de stream, waarbij de overeenkomende instanties worden vervangen door de opgegeven vervangende teken reeks.<br /><br /> **Opties**<br /><br /> patroon (type: teken reeks)-vereist.<br /><br /> vervanging (type: teken reeks)-vereist.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn) |Past normalisatie voor Perzisch toe. |  
|[Fonetiek](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Tokens maken voor fonetische overeenkomsten.<br /><br /> **Opties**<br /><br /> coderings programma (type: teken reeks)-fonetische encoder dat moet worden gebruikt. Toegestane waarden zijn onder andere: "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "Cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Standaard: "telefoon nummer". De standaard instelling is een smartphone.<br /><br /> Zie [encoder](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) voor meer informatie.<br /><br /> vervangen (type: BOOL): True als gecodeerde tokens originele tokens moeten vervangen, False als ze als synoniemen moeten worden toegevoegd. De standaard waarde is True.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Transformeert de token stroom volgens de versleutelings algoritme voor het [importeren](https://tartarus.org/~martin/PorterStemmer/). |  
|[omgekeerde](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Hiermee wordt de token teken reeks omgekeerd. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Normaliseert het gebruik van de verwisselbaar Scandinavian-tekens. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Vouwt Scandinavian tekens åÅäæÄÆ->a en öÖøØ->o. Het onderscheidt zich ook tegen het gebruik van een dubbele klinker, AA, AE, AO, OE en OO, waardoor alleen de eerste wordt verzorgd. |  
|[shingle](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Hiermee maakt u combi Naties van tokens als één token.<br /><br /> **Opties**<br /><br /> maxShingleSize (type: int): de standaard waarde is 2.<br /><br /> minShingleSize (type: int): de standaard waarde is 2.<br /><br /> outputUnigrams (type: BOOL): als deze eigenschap True is, bevat de uitvoer stroom de invoer tokens (unigrams) en shingles. De standaard waarde is True.<br /><br /> outputUnigramsIfNoShingles (type: BOOL): als deze eigenschap True is, wordt het gedrag van outputUnigrams = = False voor die tijden genegeerd wanneer er geen shingles beschikbaar zijn. De standaardwaarde is false.<br /><br /> tokenSeparator (type: teken reeks): de teken reeks die moet worden gebruikt voor het samen voegen van aangrenzende tokens om een shingle te vormen. De standaard instelling is.<br /><br /> filterToken (type: teken reeks): de teken reeks die moet worden ingevoegd voor elke positie waarop er geen token is. De standaard waarde is _.|  
|[snowball](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Snowball-token filter.<br /><br /> **Opties**<br /><br /> Language (type: String)-toegestane waarden zijn: "Armeens", "Baskisch", "Catalaans", "Deens ', ' Nederlands ', ' Engels ', ' Fins ', ' Frans ', ' Duits ', ' german2 ', ' Hong aars ', ' Italiaans ', ' Lovins ', ' Nederlands ', ', ' ', ' Portugees ', ' Roemeens ', ' Russisch ', ' Spaans ', ' Zweeds ', ' Turks '|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normaliseert de Unicode-weer gave van Sorani tekst.<br /><br /> **Opties**<br /><br /> Geen.|  
|ontleder|StemmerTokenFilter|Taalspecifiek filter voor stam gebruik.<br /><br /> **Opties**<br /><br /> Language (type: String)-toegestane waarden zijn: <br /> -   [cijfers](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   [Armeens](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   [Baskisch](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   [Braziliaan](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-"Bulgaars"<br />-   [Catalaans](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   [Tsjechië](https://portal.acm.org/citation.cfm?id=1598600)<br />-   [Denemarken](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   [Nederlandse](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   [Engelstalig](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   [Fins](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lightFinnish"<br />-   [Frans](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-"Galicisch"<br />- "minimalGalician"<br />-   [Duits](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimalGerman"<br />-   [Griekse](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-"hindi"<br />-   [Hong aars](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   [Indonesische](https://eprints.illc.uva.nl/741/2/MoL-2003-03.text.pdf)<br />-   [Iers](https://snowballstem.org/otherapps/oregan/)<br />-   [Block](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   [Letland](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   [Noors](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   [Portugese](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   [Roemeense](https://snowballstem.org/otherapps/romanian/)<br />-   [Russische](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   [Nederlandstalig](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   [Zweeds](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   [Turks](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Alle termen waarvan een woorden lijst is gesplitst, worden gemarkeerd als tref woorden, waardoor het niet mogelijk is om de keten te verlagen. Moet worden geplaatst vóór eventuele filters voor de filtering.<br /><br /> **Opties**<br /><br /> regels (type: teken reeks matrix)-opstam regels in de volgende indeling ' woord =>-stam ' bijvoorbeeld ' ran => uitvoeren '. De standaard waarde is een lege lijst.  Vereist.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Hiermee verwijdert u stop woorden uit een token stroom. Het filter gebruikt standaard een vooraf gedefinieerde lijst voor het stoppen van woorden voor Engels.<br /><br /> **Opties**<br /><br /> stopwords (type: teken reeks matrix): een lijst met stopwords. Kan niet worden opgegeven als er een stopwordsList is opgegeven.<br /><br /> stopwordsList (type: teken reeks): een vooraf gedefinieerde lijst met stopwords. Kan niet worden opgegeven als stopwords is opgegeven. Toegestane waarden zijn: ' Arabisch ', ' Armeens ', ' Baskisch ', ' Braziliaanse ', ' Bulgaars ', ' Catalaans ', ' Tsjechisch ', ' Deens ', ' Nederlands ', ' Engels ', ' Fins ', ' Frans ', ' Galicisch ', ' Duits ', ' Grieks ', ' hindi ', ' Hong aars ', ' Indonesisch ', ' Iers ', ' Italiaans ', ' Lets ', ' Noors ', ' Perzisch ', ' Portugees ', ' Roemeens ', ' Russisch ', ' Sorani ', ' Spaans ', ' Zweeds ', ' Thais ', ' Turks ', standaard: ' Engels '. Kan niet worden opgegeven als stopwords is opgegeven. <br /><br /> ignoreCase (type: BOOL): als deze eigenschap True is, worden alle woorden in kleine letters eerst als eerste gecase. De standaardwaarde is false.<br /><br /> removeTrailing (type: BOOL): als deze waarde True is, wordt de laatste zoek term genegeerd als het een stop-woord is. De standaard waarde is True.
|[gevonden](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Komt overeen met één of meerdere woord synoniemen in een token stroom.<br /><br /> **Opties**<br /><br /> synoniemen (type: teken reeks matrix)-vereist. Lijst met synoniemen in een van de volgende twee indelingen:<br /><br /> -ongelooflijke, onvoorstelbaar, Fabulous => verbluffend: alle voor waarden aan de linkerkant van => symbool worden vervangen door alle voor waarden aan de rechter kant.<br /><br /> -ongelooflijke, onvoorstelbare, Fabulous, verbluffend: een door komma's gescheiden lijst met gelijkwaardige woorden. Stel de Uitvouw optie in om te wijzigen hoe deze lijst wordt geïnterpreteerd.<br /><br /> ignoreCase (type: BOOL)-Case-vouwt invoer in voor matching. De standaardwaarde is false.<br /><br /> uitvouwen (type: BOOL): als deze optie is ingesteld op True, worden alle woorden in de lijst met synoniemen (als => notatie niet gebruikt) aan elkaar gekoppeld. <br />De volgende lijst: ongelooflijk, onvoorstelbaar, Fabulous, verbluffend is gelijk aan: ongelooflijk, onvoorstelbaar, Fabulous, verbluffend => ongelooflijke, onvoorstelbaar, Fabulous, verbluffend<br /><br />-Indien onwaar, de volgende lijst: ongelooflijk, onvoorstelbaar, Fabulous, verbluffend is gelijk aan: ongelooflijk, onvoorstelbaar, Fabulous, verbluffend => ongelooflijk.|  
|[interne](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Hiermee worden voor loop-en volg spaties van tokens verkleind. |  
|[afkappen](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|De termen worden afgekapt tot een specifieke lengte.<br /><br /> **Opties**<br /><br /> lengte (type: int)-standaard: 300, maximum: 300. Vereist.|  
|[identificatie](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Filtert tokens met dezelfde tekst als het vorige token.<br /><br /> **Opties**<br /><br /> onlyOnSamePosition (type: BOOL): als deze instelling is ingesteld, verwijdert u alleen dubbele items op dezelfde positie. De standaard waarde is True.|  
|[converteren](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(type is alleen van toepassing wanneer opties beschikbaar zijn)  |Normaliseert token tekst naar hoofd letters. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Splitst woorden in subwoorden en voert optionele trans formaties uit op subwoord groepen.<br /><br /> **Opties**<br /><br /> generateWordParts (type: BOOL)-Hiermee worden delen van woorden gegenereerd, bijvoorbeeld "AzureSearch" wordt "Azure" Search ". De standaard waarde is True.<br /><br /> generateNumberParts (type: BOOL): Hiermee worden nummer subwoorden gegenereerd. De standaard waarde is True.<br /><br /> catenateWords (type: BOOL): veroorzaakt een maximum aantal uitvoeringen van woord onderdelen voor catenated, bijvoorbeeld "Azure-Search" wordt "AzureSearch". De standaardwaarde is false.<br /><br /> catenateNumbers (type: BOOL): veroorzaakt een maximum aantal uitvoeringen van nummer delen, bijvoorbeeld "1-2" wordt "12". De standaardwaarde is false.<br /><br /> catenateAll (type: BOOL): Hiermee worden alle subwoord onderdelen catenated, bijvoorbeeld ' Azure-Search-1 ' wordt ingesteld op ' AzureSearch1 '. De standaardwaarde is false.<br /><br /> splitOnCaseChange (type: BOOL): als deze waarde True is, worden woorden op caseChange gesplitst, bijvoorbeeld "AzureSearch" wordt "Azure" Search ". De standaard waarde is True.<br /><br /> preserveOriginal: Hiermee worden oorspronkelijke woorden behouden en toegevoegd aan de lijst met subwoorden. De standaardwaarde is false.<br /><br /> splitOnNumerics (type: BOOL): als deze eigenschap waar is, wordt de waarde gesplitst op cijfers, bijvoorbeeld "Azure1Search" wordt "Azure" "1" zoeken ". De standaard waarde is True.<br /><br /> stemEnglishPossessive (type: BOOL): Hiermee wordt de volging van de voor elk subwoord verwijderd. De standaard waarde is True.<br /><br /> protectedWords (type: teken reeks matrix): tokens die u wilt beveiligen, zijn gescheiden. De standaard waarde is een lege lijst.|  

 <sup>1</sup> token filter typen worden altijd voorafgegaan door de code ' #Microsoft. Azure. Search ', zodat ' ArabicNormalizationTokenFilter ' werkelijk wordt opgegeven als ' #Microsoft. Azure. Search. ArabicNormalizationTokenFilter '.  We hebben het voor voegsel verwijderd om de breedte van de tabel te verminderen, maar vergeet niet om deze op te nemen in uw code.  

## <a name="see-also"></a>Zie ook

- [REST-Api's voor Azure Cognitive Search](/rest/api/searchservice/)
- [Analyse functies in azure Cognitive Search (voor beelden)](search-analyzers.md#examples)
- [Index maken (REST)](/rest/api/searchservice/create-index)