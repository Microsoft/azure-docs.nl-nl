---
title: Algoritme voor vergelijk Bareiteit
titleSuffix: Azure Cognitive Search
description: Het gelijkenis algoritme instellen om het nieuwe gelijkenis algoritme voor classificatie te proberen
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: e2caa09d41abb1842100ed8259e82ec411390ccb
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520626"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Classificatie algoritme in azure Cognitive Search

> [!IMPORTANT]
> Vanaf 15 juli 2020 worden voor nieuwe zoek services automatisch de functie BM25-classificatie gebruikt, die in de meeste gevallen is bewezen om zoek acties te bieden die beter zijn afgestemd op de gebruikers verwachtingen dan de huidige standaard classificatie. Naast de superieure positie biedt BM25 ook configuratie opties voor het afstemmen van resultaten op basis van factoren zoals de document grootte.  
>
> Met deze wijziging worden waarschijnlijk lichte wijzigingen in de volg orde van de zoek resultaten weer geven. Voor degenen die de impact van deze wijziging willen testen, is het BM25-algoritme beschikbaar in de API-versie 2019-05-06-preview en in 2020-06-30.  

In dit artikel wordt beschreven hoe u het nieuwe BM25-classificatie algoritme kunt gebruiken voor bestaande Zoek Services voor nieuwe indexen die worden gemaakt en opgevraagd met behulp van de preview-API.

Azure Cognitive Search is bezig met het aannemen van de officiële lucene-implementatie van het okapi BM25-algoritme, *BM25Similarity*, waarmee de eerder gebruikte *ClassicSimilarity* -implementatie wordt vervangen. Net als het oudere ClassicSimilarity-algoritme is BM25Similarity een TF-IDF-achtige ophaal functie die gebruikmaakt van de term frequentie (TF) en de inverse document frequentie (IDF) als variabelen voor het berekenen van relevantie scores voor elk document-query paar dat vervolgens wordt gebruikt voor de rang schikking. 

In het algemeen lijkt het alsof het oudere klassieke gelijkenis algoritme BM25 de hoofdmap in Probabilistic informatie op te halen om deze te verbeteren. BM25 biedt ook geavanceerde aanpassings opties, zoals het toestaan van de gebruiker om te bepalen hoe de relevantie score wordt geschaald met de term frequentie van overeenkomende voor waarden.

## <a name="how-to-test-bm25-today"></a>BM25 vandaag testen

Wanneer u een nieuwe index maakt, kunt u een **soort gelijke** eigenschappen instellen om het algoritme op te geven. U kunt de gebruiken `api-version=2019-05-06-Preview` , zoals hieronder wordt weer gegeven, of `api-version=2020-06-30` .

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
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

De eigenschap **gelijkenis** is nuttig tijdens deze tussentijdse periode wanneer beide algoritmen beschikbaar zijn, alleen op bestaande services. 

| Eigenschap | Beschrijving |
|----------|-------------|
| gelijkenis | Optioneel. Geldige waarden zijn *"#Microsoft. Azure. Search. ClassicSimilarity"* of *"#Microsoft. Azure. Search. BM25Similarity"*. <br/> Vereist `api-version=2019-05-06-Preview` of hoger voor een zoek service die is gemaakt vóór 15 juli 2020. |

Voor nieuwe services die zijn gemaakt na 15 juli 2020, wordt BM25 automatisch gebruikt en is het enige gelijkenis algoritme. Als u een **gelijkenis** wilt instellen met `ClassicSimilarity` een nieuwe service, wordt er een 400-fout geretourneerd, omdat die algoritme niet wordt ondersteund voor een nieuwe service.

Voor bestaande services die zijn gemaakt voor 15 juli 2020, blijft de klassieke gelijkenis het standaard algoritme. Als de eigenschap **gelijkenis** wordt wegge laten of op null is ingesteld, gebruikt de index het Klassieke algoritme. Als u het nieuwe algoritme wilt gebruiken, moet u **gelijkwaardigheid** instellen, zoals hierboven wordt beschreven.

## <a name="bm25-similarity-parameters"></a>BM25 gelijkenis parameters

BM25 gelijkenis voegt twee door de gebruiker aanpas bare para meters toe om de berekende relevantie score te bepalen.

### <a name="k1"></a>K1

De *K1* para meter bepaalt de schaal functie tussen de term frequentie van elke overeenkomende voor waarden en de uiteindelijke relevantie Score van een document-query paar.

Een waarde van nul staat voor een ' binair model ', waarbij de bijdrage van één overeenkomende term hetzelfde is voor alle overeenkomende documenten, ongeacht hoe vaak de term voor komt in de tekst, terwijl een grotere K1-waarde de score kan blijven verhogen naarmate er meer exemplaren van dezelfde term worden gevonden in het document. Azure Cognitive Search gebruikt standaard de waarde 1,2 voor de K1-para meter. Het gebruik van een hogere K1-waarde kan belang rijk zijn in gevallen waarin we verwachten dat er meerdere termen deel uitmaken van een zoek opdracht. In dergelijke gevallen is het mogelijk om te voor komen dat documenten die overeenkomen met veel van de verschillende query termen, worden doorzocht op documenten die slechts één keer hetzelfde zijn. Als u bijvoorbeeld een query uitvoert op de index voor documenten met de termen ' Apollo spaceflight ', is het mogelijk dat de Score van een artikel over Griekse Mythology dat de term ' Apollo ' bevat, een aantal dozijn keer, zonder vermeldingen van ' spaceflight ', in vergelijking met een ander artikel expliciet wordt vermeld, waarbij zowel ' Apollo ' als ' spaceflight ' slechts een aantal malen wordt aangegeven. 
 
### <a name="b"></a>b

De *b* -para meter bepaalt hoe de lengte van een document van invloed is op de Score van relevantie.

Een waarde van 0,0 betekent dat de lengte van het document niet van invloed is op de score, terwijl een waarde van 1,0 betekent dat de impact van de term frequentie op relevantie score wordt genormaliseerd door de lengte van het document. De standaard waarde die in azure Cognitive Search voor de b-para meter wordt gebruikt, is 0,75. Het normaliseren van de term frequentie door de lengte van het document is handig in gevallen waarin we meer documenten willen bestraffen. In sommige gevallen worden langere documenten (zoals een volledig roman) waarschijnlijk veel irrelevante voor waarden bevatten, vergeleken met veel kortere documenten.

### <a name="setting-k1-and-b-parameters"></a>K1 en b-para meters instellen

Als u de b-of K1-waarden wilt aanpassen, voegt u ze toe als eigenschappen aan het object soort gelijke objecten wanneer u BM25 gebruikt:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

Het algoritme voor gelijkenis kan alleen worden ingesteld tijdens het maken van de index. Dit betekent dat het gebruikte gelijkenis algoritme niet kan worden gewijzigd voor bestaande indexen. De para meters *"b"* en *"K1"* kunnen worden gewijzigd bij het bijwerken van een bestaande index definitie die gebruikmaakt van BM25. Als u deze waarden voor een bestaande index wijzigt, wordt de index ten minste enkele seconden offline gezet, waardoor uw indexerings-en query aanvragen mislukken. Als gevolg hiervan moet u de para meter ' allowIndexDowntime = True ' instellen in de query reeks van uw update-aanvraag:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>Zie ook  

+ [REST API referentie](/rest/api/searchservice/)
+ [Score profielen toevoegen aan uw index](index-add-scoring-profiles.md)
+ [Index-API maken](/rest/api/searchservice/create-index)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search)