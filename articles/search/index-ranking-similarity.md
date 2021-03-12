---
title: Het gelijkenis algoritme configureren
titleSuffix: Azure Cognitive Search
description: Meer informatie over hoe u BM25 kunt inschakelen voor oudere Zoek Services en hoe BM25-para meters kunnen worden aangepast om de inhoud van uw indexen beter in te passen.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 52b3523d3c092f1b9375f53038cc3b20d0ddedcc
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232831"
---
# <a name="configure-the-similarity-ranking-algorithm-in-azure-cognitive-search"></a>Het classificatie algoritme voor overeenkomsten in azure configureren Cognitive Search

Azure Cognitive Search ondersteunt twee vergelijk bare classificatie algoritmen:

+ Een *klassiek soortgelijk* algoritme, dat wordt gebruikt door alle zoek services tot en met 15 juli 2020.
+ Een implementatie van de *OKAPI BM25* -algoritme, die wordt gebruikt in alle zoek services die zijn gemaakt na 15 juli.

De BM25-classificatie is de nieuwe standaard waarde omdat deze een zoek volgorde produceert die beter is afgestemd op de verwachtingen van de gebruiker. Het bevat [para meters](#set-bm25-parameters) voor het afstemmen van resultaten op basis van factoren zoals de document grootte. 

Voor nieuwe services die zijn gemaakt na 15 juli 2020, wordt BM25 automatisch gebruikt en is het enige gelijkenis algoritme. Als u een gelijkenis wilt instellen met ClassicSimilarity op een nieuwe service, wordt er een HTTP 400-fout geretourneerd, omdat die algoritme niet wordt ondersteund door de service.

Voor oudere services die vóór 15 juli 2020 zijn gemaakt, blijft klassieke gelijkenis het standaard algoritme. Oudere Services kunnen per index worden bijgewerkt naar BM25, zoals hieronder wordt uitgelegd. Als u overschakelt van klassiek naar BM25, kunt u verwachten dat er een aantal verschillen wordt weer gegeven in de volg orde van de zoek resultaten.

> [!NOTE]
> Semantische classificatie, momenteel in de preview-versie van de standaard services in geselecteerde regio's, is een extra stap voor het produceren van meer relevante resultaten. In tegens telling tot de andere algoritmen is het een invoeg toepassing die wordt herhaald over een bestaande resultatenset. Zie [overzicht van semantische zoek acties](semantic-search-overview.md) en [semantische classificatie](semantic-ranking.md)voor meer informatie.

## <a name="enable-bm25-scoring-on-older-services"></a>BM25-scoreing inschakelen voor oudere Services

Als u een zoek service uitvoert die is gemaakt vóór 15 juli 2020, kunt u BM25 inschakelen door een soort gelijke eigenschap in te stellen voor nieuwe indexen. De eigenschap wordt alleen weer gegeven op nieuwe indexen, dus als u BM25 op een bestaande index wilt, moet u [de index verwijderen en opnieuw samen stellen](search-howto-reindex.md) met een nieuwe soort gelijke eigenschap die is ingesteld op micro soft. Azure. Search. BM25Similarity.

Als er een index met een gelijkenis eigenschap bestaat, kunt u scha kelen tussen BM25Similarity of ClassicSimilarity. 

In de volgende koppelingen wordt de eigenschap gelijkenis in de Azure-Sdk's beschreven. 

| Clientbibliotheek | Eigenschap voor soort gelijke eigenschappen |
|----------------|---------------------|
| .NET  | [SearchIndex. soort Gelijkeheid](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex.setSimilarity](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| Javascript | [SearchIndex. soort Gelijkeheid](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [gelijkenis eigenschap op SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>REST-voor beeld

U kunt ook de [rest API](/rest/api/searchservice/create-index)gebruiken, zoals in het volgende voor beeld wordt getoond:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

## <a name="set-bm25-parameters"></a>BM25-para meters instellen

BM25 gelijkenis voegt twee door de gebruiker aanpas bare para meters toe om de berekende relevantie score te bepalen. U kunt BM25-para meters instellen tijdens het maken van de index of als index-update als de BM25-algoritme is opgegeven tijdens het maken van de index.

| Eigenschap | Type | Beschrijving |
|----------|------|-------------|
| K1 | getal | Hiermee bepaalt u de schaal functie tussen de termijn frequentie van elke overeenkomende voor waarden naar de laatste relevantie Score van een document-query paar. Waarden zijn meestal 0,0 tot 3,0, met 1,2 als de standaard waarde. </br></br>Een waarde van 0,0 vertegenwoordigt een ' binair model ', waarbij de bijdrage van één overeenkomende term hetzelfde is voor alle overeenkomende documenten, ongeacht het aantal keren dat de term voor komt in de tekst, terwijl een grotere K1-waarde de score kan blijven verhogen naarmate er meer exemplaren van dezelfde term worden gevonden in het document. </br></br>Het gebruik van een hogere K1-waarde kan belang rijk zijn in gevallen waarin we verwachten dat er meerdere termen deel uitmaken van een zoek opdracht. In dergelijke gevallen is het mogelijk om te voor komen dat documenten die overeenkomen met veel van de verschillende query termen, worden doorzocht op documenten die slechts één keer hetzelfde zijn. Als u bijvoorbeeld een query uitvoert op de index voor documenten met de termen ' Apollo spaceflight ', is het mogelijk dat u de Score van een artikel over Griekse Mythology met de term ' Apollo ' in een paar dozijn keer (zonder vermeldingen van ' spaceflight ') wilt verlagen, vergeleken met een ander artikel waarin expliciet zowel ' Apollo ' als ' spaceflight ' slechts een aantal keer wordt vermeld. |
| b | getal | Hiermee wordt bepaald hoe de lengte van een document van invloed is op de Score van relevantie. Waarden liggen tussen 0 en 1, met 0,75 als de standaard waarde. </br></br>Een waarde van 0,0 betekent dat de lengte van het document niet van invloed is op de score, terwijl een waarde van 1,0 betekent dat de impact van de term frequentie op relevantie score wordt genormaliseerd door de lengte van het document. </br></br>Het normaliseren van de term frequentie door de lengte van het document is handig in gevallen waarin we meer documenten willen bestraffen. In sommige gevallen worden langere documenten (zoals een volledig roman) waarschijnlijk veel irrelevante voor waarden bevatten, vergeleken met veel kortere documenten. |

### <a name="setting-k1-and-b-parameters"></a>K1 en b-para meters instellen

Als u b-of K1-waarden wilt instellen of wijzigen, voegt u deze toe aan het object BM25 gelijkenis. Als u deze waarden voor een bestaande index instelt of wijzigt, wordt de index ten minste enkele seconden offline gezet, waardoor actieve indexeringen en query aanvragen mislukken. Daarom moet u de para meter ' allowIndexDowntime = True ' van de update-aanvraag instellen:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>Zie ook  

+ [REST API referentie](/rest/api/searchservice/)
+ [Score profielen toevoegen aan uw index](index-add-scoring-profiles.md)
+ [Index-API maken](/rest/api/searchservice/create-index)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search)