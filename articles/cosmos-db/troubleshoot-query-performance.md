---
title: Query-problemen bij het gebruik van Azure Cosmos DB oplossen
description: Meer informatie over het identificeren, vaststellen en oplossen van problemen met Azure Cosmos DB SQL-query's.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/16/2021
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 6701a580cbe7790dcce2cbbcc46889f9dff00107
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559989"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Query-problemen bij het gebruik van Azure Cosmos DB oplossen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt een algemene aanbevolen benadering besproken voor het oplossen van problemen met query's in Azure Cosmos DB. Hoewel u geen rekening moet houden met de stappen die in dit artikel worden beschreven, wordt een volledige bescherming tegen mogelijke query problemen opgenomen, maar hier zijn de meest voorkomende tips voor het verbeteren van de prestaties. Gebruik dit artikel als uitgangspunt voor het oplossen van problemen met trage of dure query's in de Azure Cosmos DB core-API (SQL). U kunt ook [diagnostische logboeken](cosmosdb-monitor-resource-logs.md) gebruiken om te bepalen welke query's langzaam zijn of welke query's aanzienlijke hoeveelheden doorvoer gebruiken. Als u de API van Azure Cosmos DB gebruikt voor MongoDB, Azure Cosmos DB gebruikt u [de API voor MongoDb voor het oplossen van query's](mongodb-troubleshoot-query.md) .

Query optimalisaties in Azure Cosmos DB zijn breed ingedeeld als volgt:

- Optimalisaties die de kosten voor de aanvraag eenheid (RU) van de query verminderen
- Optimalisaties die alleen de latentie verminderen

Als u de RU-kosten van een query reduceert, vermindert u doorgaans ook de latentie.

In dit artikel vindt u voor beelden die u opnieuw kunt maken met behulp van de [voedings gegevensset](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

## <a name="common-sdk-issues"></a>Veelvoorkomende SDK-problemen

Voordat u deze hand leiding leest, moet u rekening houden met veelvoorkomende SDK-problemen die niet zijn gerelateerd aan de query-engine.

- Volg deze [Tips voor SDK-prestaties](performance-tips.md).
    - [Probleemoplossings gids voor .NET SDK](troubleshoot-dot-net-sdk.md)
    - [Gids voor probleem oplossing voor Java SDK](troubleshoot-java-sdk-v4-sql.md)
- Met de SDK kunt `MaxItemCount` u een voor uw query's instellen, maar u kunt geen minimum aantal items opgeven.
    - Code moet elk pagina formaat afhandelen van 0 tot en met `MaxItemCount` .
- Soms bevatten query's lege pagina's, zelfs wanneer er resultaten zijn op een toekomstige pagina. Dit kan de volgende oorzaken hebben:
    - De SDK kan meerdere netwerk aanroepen uitvoeren.
    - Het kan lang duren voordat de query de documenten heeft opgehaald.
- Alle query's hebben een vervolg token waarmee de query kan worden voortgezet. Zorg ervoor dat u de query volledig leegmaakt. Meer informatie over het [verwerken van meerdere pagina's met resultaten](sql-query-pagination.md#handling-multiple-pages-of-results)

## <a name="get-query-metrics"></a>Metrische query gegevens ophalen

Wanneer u een query in Azure Cosmos DB optimaliseert, is de eerste stap altijd om [de metrische gegevens van de query](profile-sql-api-query.md) voor uw query op te halen. Deze metrische gegevens zijn ook beschikbaar via de Azure Portal. Zodra u de query in de Data Explorer hebt uitgevoerd, zijn de metrische gegevens van de query zichtbaar naast het tabblad **resultaten** :

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="Metrische query gegevens ophalen" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

Nadat u de metrische gegevens van de query hebt opgehaald, vergelijkt u het **opgehaalde aantal documenten** met het **aantal uitvoer documenten** voor uw query. Gebruik deze vergelijking om de relevante secties te identificeren die u in dit artikel kunt controleren.

Het **opgehaalde document telt** het aantal documenten dat de query-engine nodig heeft om te laden. Het **uitvoer document telt** het aantal documenten dat nodig was voor de resultaten van de query. Als het **aantal opgehaalde documenten** aanzienlijk hoger is dan het **aantal uitvoer documenten**, was er ten minste één deel van de query die geen index kon gebruiken en dat nodig is om een scan uit te voeren.

Raadpleeg de volgende secties om inzicht te krijgen in de relevante query optimalisaties voor uw scenario.

### <a name="querys-ru-charge-is-too-high"></a>De RU-kosten van de query zijn te hoog

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Het opgehaalde document aantal is aanzienlijk hoger dan het aantal uitvoer documenten

- [Neem de benodigde paden op in het indexerings beleid.](#include-necessary-paths-in-the-indexing-policy)

- [Begrijpen welke systeem functies de index gebruiken.](#understand-which-system-functions-use-the-index)

- [De uitvoering van de teken reeks systeem functie verbeteren.](#improve-string-system-function-execution)

- [Begrijp welke statistische query's de index gebruiken.](#understand-which-aggregate-queries-use-the-index)

- [Optimaliseer query's met zowel een filter als een component ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Optimaliseer de KOPPELINGs expressies met behulp van een subquery.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Het opgehaalde document aantal is ongeveer gelijk aan het aantal uitvoer documenten

- [Minimaliseer Kruis partitie query's.](#minimize-cross-partition-queries)

- [Query's met filters op meerdere eigenschappen optimaliseren.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Optimaliseer query's met zowel een filter als een component ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>De RU-kosten van de query zijn acceptabel, maar de latentie is nog te hoog

- [Verg root de nabijheid.](#improve-proximity)

- [Verhoog de ingerichte door voer.](#increase-provisioned-throughput)

- [Verg root MaxConcurrency.](#increase-maxconcurrency)

- [Verg root MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Query's waarbij het aantal opgehaalde documenten groter is dan het aantal uitvoer documenten

 Het **opgehaalde document telt** het aantal documenten dat de query-engine nodig heeft om te laden. Het **uitvoer document telt** het aantal documenten dat door de query wordt geretourneerd. Als het **aantal opgehaalde documenten** aanzienlijk hoger is dan het **aantal uitvoer documenten**, was er ten minste één deel van de query die geen index kon gebruiken en dat nodig is om een scan uit te voeren.

Hier volgt een voor beeld van een scan query die niet volledig door de index is geleverd:

Query:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Metrische gegevens van query:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Het **opgehaalde document aantal** (60.951) is aanzienlijk hoger dan het **aantal uitvoer documenten** (7), wat inhoudt dat deze query heeft geresulteerd in een document scan. In dit geval gebruikt de systeem functie [Upper ()](sql-query-upper.md) geen index.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Benodigde paden in het indexerings beleid toevoegen

Het indexerings beleid moet betrekking hebben op alle eigenschappen in `WHERE` componenten, componenten en de `ORDER BY` `JOIN` meeste systeem functies. De gewenste paden die zijn opgegeven in het index beleid moeten overeenkomen met de eigenschappen in de JSON-documenten.

> [!NOTE]
> Eigenschappen in Azure Cosmos DB indexerings beleid zijn hoofdletter gevoelig

Als u de volgende eenvoudige query uitvoert op de [voedings](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) gegevensset, ziet u een veel lagere ru-kosten wanneer de eigenschap in de `WHERE` component wordt geïndexeerd:

#### <a name="original"></a>Oorspronkelijk

Query:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
```

Indexerings beleid:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Ru-kosten:** 409,51 RUs

#### <a name="optimized"></a>Geoptimaliseerd

Bijgewerkt indexerings beleid:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Ru-kosten:** 2,98 RUs

U kunt op elk gewenst moment eigenschappen toevoegen aan het indexerings beleid, zonder dat dit van invloed is op de beschik baarheid voor schrijven of lezen. U kunt de voortgang van de [index transformatie volgen](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-system-functions-use-the-index"></a>Begrijpen welke systeem functies de index gebruiken

De meeste systeem functies gebruiken indexen. Hier volgt een lijst met enkele veelgebruikte teken reeks functies die gebruikmaken van indexen:

- StartsWith
- Contains
- RegexMatch
- Links
- Subtekenreeks, maar alleen als de eerste num_expr 0 is

Hieronder volgen enkele algemene systeem functies die de index niet gebruiken en elk document moeten worden geladen in een- `WHERE` component:

| **Systeem functie**                     | **Ideeën voor optimalisatie**             |
| --------------------------------------- |------------------------------------------------------------ |
| Bovenste/onderste                         | In plaats van de systeem functie te gebruiken voor het normaliseren van gegevens voor vergelijkingen, normaliseert u de behuizing bij het invoegen. Een query zoals dat ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` wordt ```SELECT * FROM c WHERE c.name = 'BOB'``` . |
| GetCurrentDateTime/GetCurrentTimestamp/GetCurrentTicks | Bereken de huidige tijd voordat de query wordt uitgevoerd en gebruik deze teken reeks waarde in de- `WHERE` component. |
| Wiskundige functies (non-aggregaties) | Als u een waarde regel matig in uw query wilt berekenen, kunt u overwegen om de waarde op te slaan als een eigenschap in het JSON-document. |

Deze systeem functies kunnen indexen gebruiken, behalve wanneer ze worden gebruikt in query's met aggregaties:

| **Systeem functie**                     | **Ideeën voor optimalisatie**             |
| --------------------------------------- |------------------------------------------------------------ |
| Ruimtelijke systeem functies                        | Het query resultaat opslaan in een realtime weer gave |

Bij gebruik in de `SELECT` -component heeft inefficiënte systeem functies geen invloed op de manier waarop query's indexen kunnen gebruiken.

### <a name="improve-string-system-function-execution"></a>De uitvoering van de teken reeks systeem functie verbeteren

Voor sommige systeem functies die gebruikmaken van indexen, kunt u de uitvoering van query's verbeteren door een `ORDER BY` component aan de query toe te voegen. 

Meer in het bijzonder, een systeem functie waarvan de RU-kosten toenemen naarmate de kardinaliteit van de eigenschap toeneemt, kan het voor deel zijn van `ORDER BY` de query. Deze query's voeren een index scan uit, dus als u de query resultaten sorteert, kan de query efficiënter worden.

Deze optimalisatie kan de uitvoering van de volgende systeem functies verbeteren:

- StartsWith (indien hoofdletter gevoelig = True)
- StringEquals (indien hoofdletter gevoelig = True)
- Contains
- RegexMatch
- EndsWith

Denk bijvoorbeeld aan de onderstaande query met `CONTAINS` . `CONTAINS` maakt gebruik van indexen maar soms, zelfs nadat de relevante index is toegevoegd, kunt u nog steeds een zeer hoge RU-kosten bekijken wanneer u de onderstaande query uitvoert.

Oorspronkelijke query:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
```

U kunt de uitvoering van query's verbeteren door het volgende toe te voegen `ORDER BY` :

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
ORDER BY c.town
```

Dezelfde optimalisatie kan helpen bij query's met extra filters. In dit geval is het raadzaam om ook eigenschappen met gelijkheids filters aan de component toe te voegen `ORDER BY` .

Oorspronkelijke query:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
```

U kunt de uitvoering van query's verbeteren `ORDER BY` door [een samengestelde index](index-policy.md#composite-indexes) toe te voegen voor (c.name, c. stad):

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
ORDER BY c.name, c.town
```

### <a name="understand-which-aggregate-queries-use-the-index"></a>Begrijpen welke statistische query's de index gebruiken

In de meeste gevallen zal statistische systeem functies in Azure Cosmos DB de index gebruiken. Afhankelijk van de filters of extra componenten in een statistische query, kan de query-engine echter nodig zijn om een groot aantal documenten te laden. Normaal gesp roken worden er filters voor gelijkheid en bereik toegepast op de query-engine. Na het Toep assen van deze filters kan de query-engine aanvullende filters evalueren en de resterende documenten zo nodig laden om de aggregatie te berekenen.

Als deze twee voorbeeld query's worden gebruikt, is de query met zowel een gelijkheid-als `CONTAINS` systeem functie filter doorgaans efficiënter dan een query met een filter voor een `CONTAINS` systeem functie. Dit komt doordat het gelijkheids filter eerst wordt toegepast en de index gebruikt voordat documenten moeten worden geladen voor het duurder `CONTAINS` filter.

Query met alleen `CONTAINS` filter-hogere ru-kosten:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Query met zowel een gelijkheids filter als een `CONTAINS` filter lagere ru-kosten:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Hier vindt u meer voor beelden van statistische query's waarbij de index niet volledig wordt gebruikt:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Query's met systeem functies die de index niet gebruiken

Raadpleeg de pagina van de relevante [systeem functie](sql-query-system-functions.md) om te zien of deze gebruikmaakt van de index.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Query's samen stellen met door de gebruiker gedefinieerde functies (UDF)

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Query's met GROUP BY

De RU-kosten van query's met `GROUP BY` worden verhoogd naarmate de kardinaliteit van de eigenschappen in de `GROUP BY` component toeneemt. In de onderstaande query worden de RU-kosten van de query verhoogd naarmate het aantal unieke beschrijvingen toeneemt.

De RU-kosten van een statistische functie met een- `GROUP BY` component zullen hoger zijn dan de ru-kosten van een statistische functie. In dit voor beeld moet de query-engine elk document laden dat overeenkomt met het `c.foodGroup = "Sausages and Luncheon Meats"` filter, zodat de ru-kosten naar verwachting hoog zijn.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Als u van plan bent om dezelfde statistische query's vaak uit te voeren, is het wellicht efficiënter om een realtime weer gave te bouwen met de [Azure Cosmos DB wijzigings feed](change-feed.md) dan het uitvoeren van afzonderlijke query's.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Query's met zowel een filter als een ORDER BY-component optimaliseren

Hoewel query's met een filter en een `ORDER BY` component normaal gesp roken gebruikmaken van een bereik index, zijn ze efficiënter als ze kunnen worden bediend vanuit een samengestelde index. Naast het wijzigen van het indexerings beleid, moet u alle eigenschappen in de samengestelde index toevoegen aan de- `ORDER BY` component. Met deze wijziging in de query wordt ervoor gezorgd dat de samengestelde index wordt gebruikt.  U kunt het effect observeren door een query uit te voeren op de [voedings](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) gegevensset:

#### <a name="original"></a>Oorspronkelijk

Query:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
```

Indexerings beleid:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Ru-kosten:** 44,28 RUs

#### <a name="optimized"></a>Geoptimaliseerd

Bijgewerkte query (bevat beide eigenschappen in de `ORDER BY` component):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Bijgewerkt indexerings beleid:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Ru-kosten:** 8,86 RUs

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Expressies voor samen voegen optimaliseren met behulp van een subquery

Subquery's met meerdere waarden kunnen expressies optimaliseren `JOIN` door predikaten te pushen na elke Select-veel'-expressie in plaats van na alle cross-join's in de `WHERE` component.

Bekijk deze query:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Ru-kosten:** 167,62 RUs

Voor deze query komt de index overeen met een document met een tag met de naam `infant formula` , `nutritionValue` groter dan 0 en `amount` groter dan 1. De `JOIN` expressie voert het cross-product uit van alle items met tags, nutriënten en bevat matrices voor elk overeenkomend document voordat een filter wordt toegepast. De `WHERE` component past vervolgens het filter predicaat op elke tuple toe `<c, t, n, s>` .

Als bijvoorbeeld een overeenkomend document 10 items in elk van de drie matrices heeft, wordt dit uitgebreid tot 1 x 10 x 10 x 10 (1.000) Tuples. Het gebruik van subquery's kan worden gebruikt om gekoppelde matrix items te filteren voordat u met de volgende expressie samenvoegt.

Deze query is gelijk aan de voor gaande, maar gebruikt subquery's:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Ru-kosten:** 22,17 RUs

Stel dat er slechts één item in de matrix Tags overeenkomt met het filter en dat er vijf items zijn voor de nutriënten en voor het leveren van matrices. De `JOIN` expressies worden uitgebreid tot 1 x 1 x 5 x 5 = 25 items, in tegens telling tot 1.000 items in de eerste query.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Query's waarbij het aantal opgehaalde documenten gelijk is aan het aantal uitvoer documenten

Als het **aantal opgehaalde documenten** ongeveer gelijk is aan het **aantal uitvoer documenten**, moet de query-engine veel onnodige documenten niet scannen. Voor veel query's, zoals die van het `TOP` tref woord, kan het aantal **opgehaalde documenten** groter zijn dan het **aantal uitvoer documenten** door 1. U hoeft hiervoor geen zorgen te maken.

### <a name="minimize-cross-partition-queries"></a>Kruis partitie query's minimaliseren

Azure Cosmos DB maakt gebruik van [partitionering](partitioning-overview.md) om afzonderlijke containers te schalen als aanvraag eenheid en gegevens opslag moeten toenemen. Elke fysieke partitie heeft een afzonderlijke en onafhankelijke index. Als uw query een gelijkheids filter heeft dat overeenkomt met de partitie sleutel van uw container, moet u alleen de index van de relevante partitie controleren. Deze optimalisatie vermindert het totale aantal RUs dat nodig is voor de query.

Als u een groot aantal ingerichte RUs (meer dan 30.000) of een grote hoeveelheid opgeslagen gegevens (meer dan ongeveer 100 GB) hebt, hebt u waarschijnlijk een grote voldoende container om een aanzienlijke verlaging van de query-RU-kosten te zien.

Als u bijvoorbeeld een container met de partitie sleutel foodGroup maakt, moeten de volgende query's alleen één fysieke partitie controleren:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Query's met een `IN` filter met de partitie sleutel worden alleen de relevante fysieke partitie (s) gecontroleerd en worden niet ' uitwaaieren ':

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Query's die bereik filters hebben op de partitie sleutel of die geen filters hebben op de partitie sleutel, moeten ' uitwaaieren ' hebben en de index van elke fysieke partitie controleren op resultaten:

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Query's met filters op meerdere eigenschappen optimaliseren

Hoewel query's met filters voor meerdere eigenschappen normaal gesp roken gebruikmaken van een bereik index, zijn ze efficiënter als ze kunnen worden geleverd vanuit een samengestelde index. Voor kleine hoeveel heden gegevens heeft deze optimalisatie geen aanzienlijke gevolgen. Het kan echter nuttig zijn om grote hoeveel heden gegevens te gebruiken. U kunt Maxi maal één niet-gelijkheids filter per samengestelde index optimaliseren. Als uw query meerdere filters heeft die niet gelijk zijn aan gelijkheid, kiest u een van de query's die de samengestelde index gaan gebruiken. De rest zal de bereik indexen blijven gebruiken. Het niet-gelijkheids filter moet als laatste worden gedefinieerd in de samengestelde index. Meer [informatie over samengestelde indexen](index-policy.md#composite-indexes).

Hier volgen enkele voor beelden van query's die kunnen worden geoptimaliseerd met een samengestelde index:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Hier volgt de relevante samengestelde index:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimalisaties die de latentie van query's beperken

In veel gevallen kunnen de RU-kosten acceptabel zijn wanneer de latentie van de query nog te hoog is. De volgende secties geven een overzicht van tips voor het verminderen van de latentie van query's. Als u dezelfde query meerdere keren op dezelfde gegevensset uitvoert, heeft het normaal gesp roken elke keer dezelfde RU-kosten. Maar de query latentie kan verschillen tussen de uitvoering van query's.

### <a name="improve-proximity"></a>Proximity verhogen

Query's die worden uitgevoerd vanuit een andere regio dan het Azure Cosmos DB account, hebben een hogere latentie dan wanneer ze in dezelfde regio werden uitgevoerd. Als u bijvoorbeeld code uitvoert op uw desktop computer, verwacht u dat latentie tien tallen of honderden milliseconden hoger is (of meer) dan als de query afkomstig is van een virtuele machine in dezelfde Azure-regio als Azure Cosmos DB. Het is eenvoudig om [gegevens wereld wijd te distribueren in azure Cosmos DB](distribute-data-globally.md) om ervoor te zorgen dat uw gegevens dichter bij uw app kunnen worden gebracht.

### <a name="increase-provisioned-throughput"></a>Ingerichte door Voer verhogen

In Azure Cosmos DB wordt uw ingerichte door Voer gemeten in aanvraag eenheden (RUs). Stel dat u een query hebt die 5 RUs van door Voer gebruikt. Als u bijvoorbeeld 1.000 RUs inricht, kunt u die query 200 keer per seconde uitvoeren. Als u probeert de query uit te voeren terwijl er onvoldoende door Voer beschikbaar is, retourneert Azure Cosmos DB een HTTP 429-fout. Een van de huidige core-api's (SQL) API-Sdk's voert deze query automatisch opnieuw uit nadat u een korte periode hebt gewacht. Vertraagde aanvragen nemen meer tijd in beslag, waardoor de ingerichte door Voer zo kan worden uitgebreid met de query latentie. U kunt het [totale aantal vertraagde aanvragen](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) bekijken op de Blade **metrische gegevens** van de Azure Portal.

### <a name="increase-maxconcurrency"></a>MaxConcurrency verhogen

Parallelle query's werken door meerdere partities parallel te doorzoeken. Maar gegevens van een afzonderlijke gepartitioneerde verzameling worden serieel opgehaald ten opzichte van de query. Als u MaxConcurrency instelt op het aantal partities, hebt u dus de beste kans om de meest uitvoering van de query te bereiken, mits alle andere systeem omstandigheden hetzelfde blijven. Als u het aantal partities niet weet, kunt u MaxConcurrency (of MaxDegreesOfParallelism in oudere SDK-versies) instellen op een hoog nummer. Het systeem kiest het minimum (aantal partities, door de gebruiker opgegeven invoer) als de maximale mate van parallelle uitvoering.

### <a name="increase-maxbuffereditemcount"></a>MaxBufferedItemCount verhogen

Query's zijn ontworpen om resultaten vooraf op te halen terwijl de huidige batch met resultaten door de client wordt verwerkt. Het vooraf ophalen helpt de algehele latentie van een query te verbeteren. Het instellen van MaxBufferedItemCount beperkt het aantal vooraf opgehaalde resultaten. Als u deze waarde instelt op het verwachte aantal geretourneerde resultaten (of een hoger getal), kan de query het meest voor deel krijgen van het vooraf ophalen. Als u deze waarde instelt op-1, bepaalt het systeem automatisch het aantal items dat in de buffer wordt opgeslagen.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over het meten van RUs per query, het ophalen van uitvoerings statistieken voor het afstemmen van uw query's en meer:

* [Metrische gegevens over SQL-query uitvoering ophalen met behulp van .NET SDK](profile-sql-api-query.md)
* [Queryprestaties afstemmen met Azure Cosmos DB](./sql-api-query-metrics.md)
* [Tips voor betere prestaties voor de .NET-SDK](performance-tips.md)
* [Tips voor betere prestaties voor Java v4 SDK](performance-tips-java-sdk-v4-sql.md)