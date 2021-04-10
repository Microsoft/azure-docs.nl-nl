---
title: Tekst normalisatie voor filters, facetten, sorteren
titleSuffix: Azure Cognitive Search
description: U kunt normalisatie functies opgeven in tekst velden in een index om het strikte Zoek gedrag van het sleutel woord te wijzigen in filteren, facetten en sorteren.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 3e7a33d9213d7af44d2cfc50baa847534618f7e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609219"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>Tekst normalisatie voor niet-hoofdletter gevoelige filters, facetten en sorteren

 > [!IMPORTANT]
 > Normalisatie is in open bare preview, beschikbaar via de **2020-06-30-preview-rest API**. Preview-functies worden onder aanvullende gebruiks voorwaarden aangeboden.

Voor het zoeken en ophalen van documenten uit een Azure Cognitive Search-index moet de query worden vergeleken met de inhoud van het document. De inhoud kan worden geanalyseerd om tokens te produceren voor overeenkomende als is het geval wanneer de `search` para meter wordt gebruikt, of kan worden gebruikt als-is voor strikt trefwoord vergelijking zoals gezien met `$filter` , `facets` en `$orderby` . Dit alles-of-niets-benadering is van toepassing op de meeste scenario's, maar geldt ook voor een kortere voor bereiding zoals het hoofdletter gebruik, het verwijderen van accenten, asciifolding, enzovoort.

Bekijk de volgende voorbeelden:

+ `$filter=City eq 'Las Vegas'` retourneert alleen documenten die de exacte tekst "Las Verlaag" bevatten en documenten uitsluiten met "LAS en Las", wat niet voldoende is wanneer alle documenten, ongeacht de behuizing, worden gebruikt.

+ `search=*&facet=City,count:5` retourneert "Las-houden", "LAS-onwaar" en "Las-onder" als afzonderlijke waarden, ondanks dezelfde plaats.

+ `search=usa&$orderby=City` retourneert de steden in lexicographical-volg orde: "Las onwaar", "Seattle", "Las.", zelfs als de bedoeling is om dezelfde steden samen te brengen, ongeacht het geval. 

## <a name="normalizers"></a>Normalisatie

Een *normalisatie* is een onderdeel van de zoek machine die verantwoordelijk is voor de vooraf verwerkende tekst voor het vergelijken van tref woorden. Normalisaties zijn vergelijkbaar met analyse functies, behalve dat ze de query niet Tokenize. Enkele trans formaties die kunnen worden bereikt met behulp van normalisaties zijn:

+ Converteren naar kleine letters of hoofd letters.
+ U kunt accenten en diakritische tekens, zoals ö of ê, normaliseren naar de ASCII-equivalenten "o" en "e".
+ Wijs tekens als `-` en witruimte toe aan een door de gebruiker opgegeven teken.

Normalisatie functies kunnen worden opgegeven voor tekst velden in de index en worden toegepast bij het indexeren en uitvoeren van query's.

## <a name="predefined-and-custom-normalizers"></a>Vooraf gedefinieerde en aangepaste normalisaties 

Azure Cognitive Search ondersteunt vooraf gedefinieerde normalisatie functies voor algemene use-cases, samen met de mogelijkheid om indien nodig aan te passen.

| Categorie | Beschrijving |
|----------|-------------|
| [Vooraf gedefinieerde normalisaties](#predefined-normalizers) | Is out-of-the-box, en kan zonder configuratie worden gebruikt. |
|[Aangepaste normalisaties](#add-custom-normalizers) | Voor geavanceerde scenario's. Vereist een door de gebruiker gedefinieerde configuratie van een combi natie van bestaande elementen, bestaande uit char-en Token filters. <sup>1</sup>|

<sup>(1)</sup> voor aangepaste normalisaties worden geen tokenizers opgegeven, aangezien normalisaties altijd één token moeten produceren.

## <a name="how-to-specify-normalizers"></a>Normalisaties opgeven

Normalisatie kan worden opgegeven per veld in tekst velden ( `Edm.String` en `Collection(Edm.String)` ) waarvoor ten minste één van de `filterable` `sortable` Eigenschappen, of is `facetable` ingesteld op True. Het instellen van een normalisatie is optioneel en is `null` standaard. We raden u aan om vooraf gedefinieerde normalisatie functies te evalueren voordat u een aangepaste configuratie configureert voor gebruiks gemak. Probeer een andere normalisatie als er geen resultaten worden verwacht.

Normalisatie functies kunnen alleen worden opgegeven wanneer een nieuw veld wordt toegevoegd aan de index. Het wordt aangeraden om de normalisatie vereisten vooraf te beoordelen en normalisatieers toe te wijzen in de eerste ontwikkelings stadia bij het verwijderen en opnieuw maken van indexen de routine is. Er kunnen geen normalisatie worden opgegeven voor een veld dat al is gemaakt.

1. Stel bij het maken van een veld definitie in de [index](/rest/api/searchservice/create-index)de eigenschap  **normalisatie** in op een van de volgende: een [vooraf gedefinieerde normalisatie](#predefined-normalizers) `lowercase` , zoals of een aangepaste normalisatie functie (gedefinieerd in hetzelfde index schema).  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. Aangepaste normalisatie functies moeten eerst worden gedefinieerd in de sectie **[normalisaties]** van de index en vervolgens worden toegewezen aan de definitie van het veld, zoals weer gegeven in de vorige stap. Zie [index maken](/rest/api/searchservice/create-index) en [aangepaste normalisatie functies toevoegen](#add-custom-normalizers)voor meer informatie.


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> Als u de normalisatie van een bestaand veld wilt wijzigen, moet u de index volledig opnieuw samen stellen (u kunt geen afzonderlijke velden opnieuw samen stellen).

Een goede oplossing voor productie-indexen, waarbij indexen opnieuw worden gemaakt, is het maken van een nieuw veld dat identiek is aan het oude, maar met de nieuwe normalisatie functie, en deze gebruiken in plaats van de oude. Gebruik [update-index](/rest/api/searchservice/update-index) om het nieuwe veld en [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) op te nemen. Later kunt u als onderdeel van de geplande index onderhoud de index opschonen om verouderde velden te verwijderen.

## <a name="add-custom-normalizers"></a>Aangepaste normalisaties toevoegen

Aangepaste normalisatie functies worden gedefinieerd binnen het index schema en kunnen worden opgegeven met behulp van de veld eigenschap. De definitie van aangepaste normalisatie bevat een naam, een type, een of meer teken filters en Token filters. De teken filters en Token filters zijn de bouw stenen voor een aangepaste normalisatie die verantwoordelijk is voor de verwerking van de tekst. Deze filters worden van links naar rechts toegepast.

 De `token_filter_name_1` is de naam van het token filter en `char_filter_name_1` `char_filter_name_2` de namen van de teken filters (Zie [ondersteunde token filters](#supported-token-filters) en gefilterde tekens tabellen hieronder voor geldige waarden).

De normalisatie definitie maakt deel uit van de grotere index. Zie [Create Index-API](/rest/api/searchservice/create-index) voor informatie over de rest van de index.

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Aangepaste normalisatie functies kunnen worden toegevoegd tijdens het maken van de index of later door het bijwerken van een bestaande. Als u een aangepaste normalisatie functie aan een bestaande index wilt toevoegen, moet de **allowIndexDowntime** -vlag worden opgegeven in de [update-index](/rest/api/searchservice/update-index) en zal de index enkele seconden niet beschikbaar zijn.

## <a name="normalizers-reference"></a>Naslag informatie over normalisatie

### <a name="predefined-normalizers"></a>Vooraf gedefinieerde normalisaties
|**Naam**|**Beschrijving en opties**|  
|-|-|  
|Standard| Kleine letters de tekst gevolgd door asciifolding.|  
|kleine| Transformeert tekens naar kleine letters.|
|converteren| Transformeert tekens naar hoofd letters.|
|asciifolding| Transformeert tekens die geen deel uitmaken van het Latijnse Unicode-basis blok naar hun ASCII-equivalent, als er een bestaat. Bijvoorbeeld een wijziging à naar een.|
|elision| Verwijdert Elision uit het begin van de tokens.|

### <a name="supported-char-filters"></a>Ondersteunde teken filters
Raadpleeg voor meer informatie over de teken filters [Naslag informatie over teken filters](index-add-custom-analyzers.md#CharFilter).
+ [toewijzing](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>Ondersteunde token filters
In de onderstaande lijst ziet u de token filters die worden ondersteund voor normalisatie en is een subset van de algemene token filters die betrokken zijn bij de lexicale analyse. Raadpleeg voor meer informatie over de filters de [verwijzing naar token filters](index-add-custom-analyzers.md#TokenFilters).

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [kleine](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [converteren](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>Voor beeld van aangepaste normalisatie-voor beelden

In het onderstaande voor beeld ziet u een aangepaste normalisatie definitie met bijbehorende teken filters en Token filters. Aangepaste opties voor teken filters en Token filters worden afzonderlijk opgegeven als benoemde constructs en vervolgens naar de normalisatie definitie, zoals hieronder wordt geïllustreerd.

* Er wordt een aangepaste normalisatie functie ' my_custom_normalizer ' gedefinieerd in de `normalizers` sectie van de index definitie.
* De normalisatie bestaat uit twee teken filters en drie token filters: Elision, kleine en aangepaste asciifolding filter "my_asciifolding".
* Het eerste char-filter "map_dash" vervangt alle streepjes door onderstrepings tekens, terwijl de tweede een "remove_whitespace" alle spaties verwijdert.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
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
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>Zie ook
+ [Analyse functies voor taal kundige en tekst verwerking](search-analyzers.md)

+ [REST API voor documenten zoeken](/rest/api/searchservice/search-documents) 
