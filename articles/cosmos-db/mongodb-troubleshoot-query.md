---
title: Problemen met query's oplossen bij het gebruik van de Azure Cosmos DB-API voor MongoDB
description: Meer informatie over het identificeren, diagnosticeren en oplossen van de API van Azure Cosmos DB voor MongoDB-query problemen.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.subservice: cosmosdb-mongo
ms.date: 03/02/2021
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 5302cb7bb3f4683d200f6f9ea106991bb934fc17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659899"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>Problemen met query's oplossen bij het gebruik van de Azure Cosmos DB-API voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In dit artikel wordt een algemene aanbevolen benadering besproken voor het oplossen van problemen met query's in Azure Cosmos DB. Hoewel u geen rekening moet houden met de stappen die in dit artikel worden beschreven, wordt een volledige bescherming tegen mogelijke query problemen opgenomen, maar hier zijn de meest voorkomende tips voor het verbeteren van de prestaties. Gebruik dit artikel als uitgangs punt voor het oplossen van problemen met trage of dure query's in de API van Azure Cosmos DB voor MongoDB. Als u de Azure Cosmos DB core-API (SQL) gebruikt, raadpleegt u het artikel [probleemoplossings handleiding voor SQL-API-query's](troubleshoot-query-performance.md) .

Query optimalisaties in Azure Cosmos DB zijn breed ingedeeld als volgt:

- Optimalisaties die de kosten voor de aanvraag eenheid (RU) van de query verminderen
- Optimalisaties die alleen de latentie verminderen

Als u de RU-kosten van een query reduceert, vermindert u doorgaans ook de latentie.

In dit artikel vindt u voor beelden die u opnieuw kunt maken met behulp van de [voedings gegevensset](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

> [!NOTE] 
> In dit artikel wordt ervan uitgegaan dat u de API van Azure Cosmos DB gebruikt voor MongoDB-accounts met versie 3,6 en hoger. Sommige query's die slecht in versie 3,2 worden uitgevoerd, hebben aanzienlijke verbeteringen in versies 3.6 +. Voer een upgrade uit naar versie 3,6 door een [ondersteunings aanvraag](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)in te dienen.

## <a name="use-explain-command-to-get-metrics"></a>$explain opdracht gebruiken om metrische gegevens op te halen

Wanneer u een query in Azure Cosmos DB optimaliseert, is de eerste stap altijd om [de ru-kosten](find-request-unit-charge-mongodb.md) voor uw query te verkrijgen. Als een ruwe richt lijn kunt u de kosten voor de RU verlagen voor query's met meer dan 50 RUs. 

Naast het verkrijgen van de RU-kosten, moet u de `$explain` opdracht gebruiken om de metrische gegevens voor de query en het index gebruik te verkrijgen. Hier volgt een voor beeld waarin een query wordt uitgevoerd en de opdracht wordt gebruikt `$explain` voor het weer geven van metrische gegevens over query's en index gebruik:

**$explain opdracht:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**Uitvoer**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

De `$explain` uitvoer van de opdracht is lang en bevat gedetailleerde informatie over het uitvoeren van query's. Over het algemeen zijn er echter enkele secties waarin u zich kunt richten bij het optimaliseren van de query prestaties:

| Metrisch | Beschrijving | 
| ------ | ----------- |
| `timeInclusiveMS` | Query wachttijd back-end |
| `pathsIndexed` | Bevat indexen die de query gebruikt | 
| `pathsNotIndexed` | Bevat indexen die de query zou kunnen gebruiken, indien beschikbaar | 
| `shardInformation` | Samen vatting van de query prestaties voor een bepaalde [fysieke partitie](./partitioning-overview.md#physical-partitions) | 
| `retrievedDocumentCount` | Aantal documenten dat door de query-engine is geladen | 
| `outputDocumentCount` | Aantal geretourneerde documenten in de query resultaten | 
| `estimatedDelayFromRateLimitingInMilliseconds` | Geschatte extra latentie van de query vanwege een frequentie beperking | 

Nadat u de metrische gegevens van de query hebt opgehaald, moet u deze vergelijken `retrievedDocumentCount` met de `outputDocumentCount` voor uw query. Gebruik deze vergelijking om de relevante secties te identificeren die u in dit artikel kunt controleren. Het `retrievedDocumentCount`  is het aantal documenten dat door de query-engine moet worden geladen. Het `outputDocumentCount` is het aantal documenten dat nodig was voor de resultaten van de query. Als het `retrievedDocumentCount`  groter is dan de `outputDocumentCount` , is er ten minste één deel van de query die niet kan worden gebruikt door een index en die nodig is om een scan uit te voeren.

Raadpleeg de volgende secties om inzicht te krijgen in de relevante query optimalisaties voor uw scenario.

### <a name="querys-ru-charge-is-too-high"></a>De RU-kosten van de query zijn te hoog

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>Het opgehaalde document aantal is aanzienlijk hoger dan het aantal uitvoer documenten

- [Neem de vereiste indexen op.](#include-necessary-indexes)

- [Begrijp welke aggregatie bewerkingen de index gebruiken.](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Het opgehaalde document aantal is ongeveer gelijk aan het aantal uitvoer documenten

- [Minimaliseer Kruis partitie query's.](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>De RU-kosten van de query zijn acceptabel, maar de latentie is nog te hoog

- [Verg root de nabijheid.](#improve-proximity)

- [Verhoog de ingerichte door voer.](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Query's waarbij het aantal opgehaalde documenten groter is dan het aantal uitvoer documenten

 Het `retrievedDocumentCount` is het aantal documenten dat de query-engine nodig heeft om te laden. De `outputDocumentCount` is het aantal documenten dat door de query wordt geretourneerd. Als het `retrievedDocumentCount` groter is dan de `outputDocumentCount` , is er ten minste één deel van de query die niet kan worden gebruikt door een index en die nodig is om een scan uit te voeren.

Hier volgt een voor beeld van een scan query die niet volledig door de index is geleverd:

**$explain opdracht:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**Uitvoer**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

De `retrievedDocumentCount` (8618) is aanzienlijk hoger dan de `outputDocumentCount` (1), wat inhoudt dat deze query een document Scan vereist. 

### <a name="include-necessary-indexes"></a>Vereiste indexen toevoegen

Controleer de `pathsNotIndexed` matrix en voeg deze indexen toe. In dit voor beeld worden de paden `foodGroup` en `description` geïndexeerd.

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

Het indexeren van aanbevolen procedures in de API van Azure Cosmos DB voor MongoDB wijkt af van MongoDB. In de API van Azure Cosmos DB voor MongoDB worden samengestelde indexen alleen gebruikt in query's die op meerdere eigenschappen efficiënt moeten worden gesorteerd. Als u query's met filters op meerdere eigenschappen hebt, moet u enkelvoudige veld indexen maken voor elk van deze eigenschappen. Query predikaten kunnen meerdere enkelvoudige veld indexen gebruiken.

Met [joker tekens kunnen indexen](mongodb-indexing.md#wildcard-indexes) eenvoudiger worden geïndexeerd. In tegens telling tot in MongoDB kunnen Joker teken indexen meerdere velden in query-predikaten ondersteunen. Er is geen verschil in de query prestaties als u één enkele Joker teken index gebruikt in plaats van een afzonderlijke index voor elke eigenschap te maken. Het toevoegen van een Joker teken index voor alle eigenschappen is de eenvoudigste manier om al uw query's te optimaliseren.

U kunt op elk gewenst moment nieuwe indexen toevoegen, zonder dat dit van invloed is op de beschik baarheid voor schrijven of lezen. U kunt de voortgang van de [index transformatie volgen](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-aggregation-operations-use-the-index"></a>Begrijpen welke aggregatie bewerkingen de index gebruiken

In de meeste gevallen zullen aggregatie bewerkingen in de API van Azure Cosmos DB voor MongoDB gedeeltelijk gebruikmaken van indexen. Normaal gesp roken worden in de query-engine de filters voor gelijkheid en bereik toegepast en worden er indexen gebruikt. Na het Toep assen van deze filters kan de query-engine aanvullende filters evalueren en de resterende documenten zo nodig laden om de aggregatie te berekenen. 

Hier volgt een voorbeeld:

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

In dit geval kunnen indexen de fase optimaliseren `$match` . Het toevoegen van een index voor `foodGroup` zal de query prestaties aanzienlijk verbeteren. Net als in MongoDB moet u `$match` zo snel mogelijk in de aggregatie pijplijn plaatsen om het gebruik van indexen te maximaliseren.

In de API van Azure Cosmos DB voor MongoDB worden indexen niet gebruikt voor de werkelijke aggregatie, in dit geval `$max` . Bij het toevoegen van een index op `version` worden de query prestaties niet verbeterd.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Query's waarbij het aantal opgehaalde documenten gelijk is aan het aantal uitvoer documenten

Als de `retrievedDocumentCount` is bijna gelijk is aan de `outputDocumentCount` , heeft de query-engine geen veel onnodige documenten scannen.

### <a name="minimize-cross-partition-queries"></a>Kruis partitie query's minimaliseren

Azure Cosmos DB maakt gebruik van [partitionering](partitioning-overview.md) om afzonderlijke containers te schalen als aanvraag eenheid en gegevens opslag moeten toenemen. Elke fysieke partitie heeft een afzonderlijke en onafhankelijke index. Als uw query een gelijkheids filter heeft dat overeenkomt met de partitie sleutel van uw container, moet u alleen de index van de relevante partitie controleren. Deze optimalisatie vermindert het totale aantal RUs dat nodig is voor de query. Meer [informatie over de verschillen tussen query's in de partitie en query's voor meerdere partities](how-to-query-container.md).

Als u een groot aantal ingerichte RUs (meer dan 30.000) of een grote hoeveelheid opgeslagen gegevens (meer dan ongeveer 100 GB) hebt, hebt u waarschijnlijk een grote voldoende container om een aanzienlijke verlaging van de query-RU-kosten te zien. 

U kunt de `shardInformation` matrix controleren om inzicht te krijgen in de metrische gegevens van de query voor elke afzonderlijke fysieke partitie. Het aantal unieke `shardKeyRangeId` waarden is het aantal fysieke partities waar de query moet worden uitgevoerd. In dit voor beeld is de query uitgevoerd op vier fysieke partities. Het is belang rijk om te begrijpen dat de uitvoering volledig onafhankelijk is van het index gebruik. Met andere woorden, kruis partitie query's kunnen nog steeds indexen gebruiken.

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimalisaties die de latentie van query's beperken

In veel gevallen kunnen de RU-kosten acceptabel zijn wanneer de latentie van de query nog te hoog is. De volgende secties geven een overzicht van tips voor het verminderen van de latentie van query's. Als u dezelfde query meerdere keren op dezelfde gegevensset uitvoert, heeft het normaal gesp roken elke keer dezelfde RU-kosten. Maar de query latentie kan verschillen tussen de uitvoering van query's.

### <a name="improve-proximity"></a>Proximity verhogen

Query's die worden uitgevoerd vanuit een andere regio dan het Azure Cosmos DB account, hebben een hogere latentie dan wanneer ze in dezelfde regio werden uitgevoerd. Als u bijvoorbeeld code uitvoert op uw desktop computer, verwacht u dat latentie tien tallen of honderden milliseconden hoger is (of meer) dan als de query afkomstig is van een virtuele machine in dezelfde Azure-regio als Azure Cosmos DB. Het is eenvoudig om [gegevens wereld wijd te distribueren in azure Cosmos DB](tutorial-global-distribution-mongodb.md) om ervoor te zorgen dat uw gegevens dichter bij uw app kunnen worden gebracht.

### <a name="increase-provisioned-throughput"></a>Ingerichte door Voer verhogen

In Azure Cosmos DB wordt uw ingerichte door Voer gemeten in aanvraag eenheden (RUs). Stel dat u een query hebt die 5 RUs van door Voer gebruikt. Als u bijvoorbeeld 1.000 RUs inricht, kunt u die query 200 keer per seconde uitvoeren. Als u probeert de query uit te voeren terwijl er onvoldoende door Voer beschikbaar is, wordt het aantal aanvragen beperkt Azure Cosmos DB. Azure Cosmos DB-API voor MongoDB voert deze query automatisch opnieuw uit nadat u een korte keer hebt gewacht. Vertraagde aanvragen nemen meer tijd in beslag, waardoor de ingerichte door Voer zo kan worden uitgebreid met de query latentie.

De waarde `estimatedDelayFromRateLimitingInMilliseconds` geeft een beeld van de mogelijke latentie voordelen als u de door Voer verhoogt.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met query prestaties oplossen (SQL-API)](troubleshoot-query-performance.md)
* [Indexering beheren voor de API voor MongoDB van Azure Cosmos DB](mongodb-indexing.md)
