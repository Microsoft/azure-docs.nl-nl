---
title: Query parallel Lise ring gebruiken en schalen in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u Stream Analytics taken kunt schalen door het configureren van invoer partities, het afstemmen van de query definitie en het instellen van streaming-eenheden voor taken.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 326af3bc38ce70cc7cb205384bb4302c5ff73d28
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704177"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Gebruik query parallel Lise ring in Azure Stream Analytics
In dit artikel leest u hoe u parallel Lise ring in Azure Stream Analytics kunt gebruiken. U leert hoe u Stream Analytics taken kunt schalen door het configureren van invoer partities en het afstemmen van de definitie van de analyse query.
U kunt het beste bekend zijn met het begrip van de streaming-eenheid die wordt beschreven in [begrijpen en streaming-eenheden aanpassen](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Wat zijn de onderdelen van een Stream Analytics-taak?
Een Stream Analytics taak definitie bevat ten minste één streaming-invoer, een query en uitvoer. Invoer van waaruit de taak de gegevens stroom leest. De query wordt gebruikt voor het transformeren van de gegevensinvoer stroom en de uitvoer is waarnaar de taak resultaten verzendt.

## <a name="partitions-in-inputs-and-outputs"></a>Partities in invoer en uitvoer
Met partitioneren kunt u gegevens in subsets verdelen op basis van een [partitie sleutel](../event-hubs/event-hubs-scalability.md#partitions). Als uw invoer (bijvoorbeeld Event Hubs) is gepartitioneerd met een sleutel, is het raadzaam om deze partitie sleutel op te geven wanneer u invoer toevoegt aan uw Stream Analytics-taak. Het schalen van een Stream Analytics taak maakt gebruik van partities in de invoer en uitvoer. Een Stream Analytics taak kan verschillende partities parallel gebruiken en schrijven, waardoor de door Voer wordt verhoogd. 

### <a name="inputs"></a>Invoer
Alle Azure Stream Analytics invoer kan gebruikmaken van partitionering:
-   EventHub (de partitie sleutel expliciet moet worden ingesteld met het sleutel woord PARTITION BY als u het compatibiliteits niveau 1,1 of lager gebruikt)
-   IoT Hub (moet de partitie sleutel expliciet instellen met het sleutel woord PARTITION BY als u het compatibiliteits niveau 1,1 of lager gebruikt)
-   Blob Storage

### <a name="outputs"></a>Uitvoer

Wanneer u werkt met Stream Analytics, kunt u gebruikmaken van partitionering in de uitvoer:
-   Azure Data Lake Storage
-   Azure Functions
-   Azure Table
-   Blob-opslag (kan de partitie sleutel expliciet instellen)
-   Cosmos DB (moet de partitie sleutel expliciet instellen)
-   Event Hubs (moet de partitie sleutel expliciet instellen)
-   IoT Hub (moet de partitie sleutel expliciet instellen)
-   Service Bus
- SQL en Azure Synapse Analytics met optionele partitionering: Zie voor meer informatie de [pagina uitvoer naar Azure SQL database](./stream-analytics-sql-output-perf.md).

Power BI biedt geen ondersteuning voor partitioneren. U kunt echter nog steeds de invoer partitioneren zoals beschreven in [deze sectie](#multi-step-query-with-different-partition-by-values) 

Raadpleeg de volgende artikelen voor meer informatie over partities:

* [Overzicht van functies van Event Hubs](../event-hubs/event-hubs-features.md#partitions)
* [Gegevenspartitionering](/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Lege parallelle taken
Een *verlegen parallelle* taak is het meest schaal bare scenario in azure stream Analytics. De ene partitie van de invoer wordt verbonden met één exemplaar van de query naar één partitie van de uitvoer. Deze parallellisme heeft de volgende vereisten:

1. Als uw query logica afhankelijk is van dezelfde sleutel die wordt verwerkt door hetzelfde query-exemplaar, moet u ervoor zorgen dat de gebeurtenissen naar dezelfde partitie van de invoer gaan. Voor Event Hubs of IoT Hub houdt dit in dat de waarde voor de gebeurtenis gegevens moet zijn ingesteld op **PartitionKey** . U kunt ook gepartitioneerde afzenders gebruiken. Dit betekent dat de gebeurtenissen worden verzonden naar dezelfde partitie map voor Blob Storage. Een voor beeld hiervan is een query-exemplaar waarmee gegevens per gebruikers-id worden geaggregeerd waarbij de invoer Event Hub wordt gepartitioneerd met de gebruikers-id als partitie sleutel. Als uw query logica echter niet vereist dat dezelfde sleutel wordt verwerkt door hetzelfde query-exemplaar, kunt u deze vereiste negeren. Een voor beeld van deze logica is een eenvoudige SELECT-project-filter query.  

2. De volgende stap is het maken van de query is gepartitioneerd. Voor taken met compatibiliteits niveau 1,2 of hoger (aanbevolen) kan aangepaste kolom worden opgegeven als partitie sleutel in de invoer instellingen en de taak automatisch wordt paralellized. Voor taken met compatibiliteits niveau 1,0 of 1,1 moet u **Partition by PartitionId** gebruiken in alle stappen van uw query. Meerdere stappen zijn toegestaan, maar ze moeten allemaal worden gepartitioneerd met dezelfde sleutel. 

3. Het meren deel van de uitvoer die wordt ondersteund in Stream Analytics kan gebruikmaken van partitionering. Als u een uitvoer type gebruikt dat geen ondersteuning biedt voor partitionering, wordt de taak niet *verlegen parallel*. Zorg ervoor dat de **partitie sleutel kolom** is ingesteld op dezelfde partitie sleutel die wordt gebruikt in de query voor Event hub-uitvoer. Raadpleeg de [sectie uitvoer](#outputs) voor meer informatie.

4. Het aantal invoer partities moet gelijk zijn aan het aantal uitvoer partities. Blob Storage-uitvoer kan partities ondersteunen en het partitie schema van de upstream-query overnemen. Wanneer een partitie sleutel voor Blob Storage is opgegeven, worden gegevens gepartitioneerd per invoer partitie, zodat het resultaat nog steeds volledig parallel is. Hier volgen enkele voor beelden van partitie waarden die een volledig parallelle taak toestaan:

   * 8 Event Hub invoer partities en 8 Event Hub uitvoer partities
   * 8 Event Hub invoer partities en uitvoer van Blob-opslag
   * 8 Event Hub invoer partities en uitvoer van Blob-opslag gepartitioneerd door een aangepast veld met een wille keurige kardinaliteit
   * invoer partities met 8 Blob-opslag en uitvoer van Blob-opslag
   * 8 Blob Storage-invoer partities en 8 Event Hub uitvoer partities

In de volgende secties worden enkele voorbeeld scenario's besproken die kunnen worden verlegen.

### <a name="simple-query"></a>Eenvoudige query

* Invoer: Event hub met 8 partities
* Uitvoer: Event hub met 8 partities ("partitie sleutel kolom" moet worden ingesteld op het gebruik van "PartitionId")

Query:

```SQL
    --Using compatibility level 1.2 or above
    SELECT TollBoothId
    FROM Input1
    WHERE TollBoothId > 100
    
    --Using compatibility level 1.0 or 1.1
    SELECT TollBoothId
    FROM Input1 PARTITION BY PartitionId
    WHERE TollBoothId > 100
```

Deze query is een eenvoudig filter. Daarom hoeft u zich geen zorgen te maken over het partitioneren van de invoer die wordt verzonden naar de Event Hub. Taken met een compatibiliteits niveau van vóór 1,2 moeten de component **Partition by PartitionId** bevatten, zodat de vereiste #2 van eerder is voldaan. Voor de uitvoer moeten we de Event Hub uitvoer in de taak configureren zodat de partitie sleutel is ingesteld op **PartitionId**. Een laatste controle is om ervoor te zorgen dat het aantal invoer partities gelijk is aan het aantal uitvoer partities.

### <a name="query-with-a-grouping-key"></a>Query's met een groepeer sleutel

* Invoer: Event hub met 8 partities
* Uitvoer: Blob Storage

Query:

```SQL
    --Using compatibility level 1.2 or above
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
    --Using compatibility level 1.0 or 1.1
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Deze query heeft een groepeer sleutel. Daarom moeten de gebeurtenissen die worden gegroepeerd, worden verzonden naar dezelfde event hub-partitie. Omdat in dit voor beeld we groeperen op TollBoothID, moeten we er zeker van zijn dat TollBoothID als de partitie sleutel wordt gebruikt wanneer de gebeurtenissen worden verzonden naar Event hub. In ASA kunnen we **Partition by PartitionId** gebruiken om de eigenschappen van dit partitie schema over te nemen en volledige parallel Lise ring in te scha kelen. Omdat de uitvoer Blob Storage is, hoeft u zich geen zorgen te maken over het configureren van een partitie sleutel waarde, conform de vereiste #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Voor beeld van scenario's die *niet* verlegen parallel zijn

In de vorige sectie hebben we enkele verlege en parallelle scenario's geleerd. In deze sectie bespreken we scenario's die niet voldoen aan de vereisten die u wilt verlegen parallel. 

### <a name="mismatched-partition-count"></a>Aantal partities komt niet overeen
* Invoer: Event hub met 8 partities
* Uitvoer: Event hub met 32-partities

Als het aantal invoer partities niet overeenkomt met het aantal uitvoer partities, is de topologie niet onafhankelijk van de query. We kunnen echter wel een niveau of parallel Lise ring ophalen.

### <a name="query-using-non-partitioned-output"></a>Query's uitvoeren met niet-gepartitioneerde uitvoer
* Invoer: Event hub met 8 partities
* Uitvoer: Power BI

Power BI-uitvoer biedt momenteel geen ondersteuning voor partitioneren. Daarom is dit scenario niet-verlegen parallel.

### <a name="multi-step-query-with-different-partition-by-values"></a>Meerdere stappen query's met verschillende partities op waarden
* Invoer: Event hub met 8 partities
* Uitvoer: Event hub met 8 partities
* Compatibiliteits niveau: 1,0 of 1,1

Query:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Zoals u kunt zien, gebruikt de tweede stap **TollBoothId** als de partitie sleutel. Deze stap is niet hetzelfde als de eerste stap en hiervoor moet een wille keurige volg orde worden uitgevoerd. 

### <a name="multi-step-query-with-different-partition-by-values"></a>Meerdere stappen query's met verschillende partities op waarden
* Invoer: Event hub met 8 partities
* Uitvoer: Event hub met 8 partities ("partitie sleutel kolom" moet worden ingesteld op het gebruik van "TollBoothId")
* Compatibiliteits niveau-1,2 of hoger

Query:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Met compatibiliteits niveau 1,2 of hoger wordt parallelle query-uitvoering standaard ingeschakeld. Query uit de vorige sectie wordt bijvoorbeeld gepartitioneerd op voor waarde dat de kolom "TollBoothId" is ingesteld als een invoer partitie sleutel. De component PARTITION BY PartitionId is niet vereist.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Het maximum aantal streaming-eenheden van een taak berekenen
Het totale aantal streaming-eenheden dat door een Stream Analytics taak kan worden gebruikt, is afhankelijk van het aantal stappen in de query die is gedefinieerd voor de taak en het aantal partities voor elke stap.

### <a name="steps-in-a-query"></a>Stappen in een query
Een query kan een of meerdere stappen bevatten. Elke stap is een subquery die is gedefinieerd met het sleutel woord **with** . De query die zich buiten het sleutel woord **with** bevindt (één query alleen), telt ook als een stap, zoals de **Select** -instructie in de volgende query:

Query:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

Deze query bestaat uit twee stappen.

> [!NOTE]
> Deze query wordt verderop in het artikel uitvoeriger besproken.
>  

### <a name="partition-a-step"></a>Een stap partitioneren
Voor het partitioneren van een stap zijn de volgende voor waarden vereist:

* De invoer bron moet worden gepartitioneerd. 
* De instructie **Select** van de query moet van een gepartitioneerde invoer bron worden gelezen.
* De query in de stap moet het sleutel woord **Partition by** hebben.

Wanneer een query is gepartitioneerd, worden de invoer gebeurtenissen verwerkt en geaggregeerd in afzonderlijke partitie groepen, en worden uitvoer gebeurtenissen gegenereerd voor elk van de groepen. Als u een gecombineerde aggregatie wilt, moet u een tweede niet-gepartitioneerde stap maken om samen te voegen.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Het maximum aantal streaming-eenheden voor een taak berekenen
Alle niet-gepartitioneerde stappen kunnen samen worden geschaald naar zes streaming-eenheden (SUs) voor een Stream Analytics-taak. Daarnaast kunt u voor elke partitie in een gepartitioneerde stap 6 SUs toevoegen.
In de onderstaande tabel ziet u enkele **voor beelden** .

| Query’s uitvoeren                                               | Max. SUs voor de taak |
| --------------------------------------------------- | ------------------- |
| <ul><li>De query bevat één stap.</li><li>De stap is niet gepartitioneerd.</li></ul> | 6 |
| <ul><li>De gegevens stroom voor invoer is gepartitioneerd met 16.</li><li>De query bevat één stap.</li><li>De stap is gepartitioneerd.</li></ul> | 96 (6 * 16 partities) |
| <ul><li>De query bevat twee stappen.</li><li>Geen van de stappen is gepartitioneerd.</li></ul> | 6 |
| <ul><li>De invoer gegevens stroom is gepartitioneerd door 3.</li><li>De query bevat twee stappen. De invoer stap is gepartitioneerd en de tweede stap is niet.</li><li>De <strong>Select</strong> -instructie leest uit de gepartitioneerde invoer.</li></ul> | 24 (18 voor gepartitioneerde stappen + 6 voor niet-gepartitioneerde stappen |

### <a name="examples-of-scaling"></a>Voor beelden van schalen

De volgende query berekent het aantal auto's binnen een periode van drie minuten door middel van een gratis station dat drie tollbooths heeft. Deze query kan naar Maxi maal zes SUs worden geschaald.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Als u meer SUs voor de query wilt gebruiken, moeten zowel de invoer gegevensstroom als de query worden gepartitioneerd. Omdat de gegevensstroom partitie is ingesteld op 3, kan de volgende gewijzigde query worden uitgebreid tot 18 SUs:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Wanneer een query is gepartitioneerd, worden de invoer gebeurtenissen verwerkt en geaggregeerd in afzonderlijke partitie groepen. Uitvoer gebeurtenissen worden ook gegenereerd voor elk van de groepen. Partitioneren kan onverwachte resultaten veroorzaken wanneer het veld **groeperen op** niet de partitie sleutel in de gegevens stroom voor invoer is. Het veld **TollBoothId** in de vorige query is bijvoorbeeld niet de partitie sleutel van **input1**. Het resultaat is dat de gegevens van TollBooth #1 in meerdere partities kunnen worden verdeeld.

Elk van de **input1** -partities wordt afzonderlijk verwerkt door stream Analytics. Als gevolg hiervan worden meerdere records van het aantal auto's voor dezelfde tollbooth in hetzelfde Tumblingvenstertriggers-venster gemaakt. Als de invoer partitie sleutel niet kan worden gewijzigd, kan dit probleem worden opgelost door een niet-partitie stap toe te voegen aan aggregatie waarden voor verschillende partities, zoals in het volgende voor beeld:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Deze query kan worden geschaald naar 24 SUs.

> [!NOTE]
> Als u twee streams koppelt, moet u ervoor zorgen dat de streams worden gepartitioneerd door de partitie sleutel van de kolom die u gebruikt om de joins te maken. Zorg er ook voor dat u hetzelfde aantal partities in beide streams hebt.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Hoge door Voer op schaal bereiken

Een [verlegen parallelle](#embarrassingly-parallel-jobs) taak is nodig, maar niet voldoende voor een hogere door Voer op schaal. Elk opslag systeem en de bijbehorende Stream Analytics uitvoer hebben afwijkingen voor het behalen van de best mogelijke schrijf doorvoer. Net als bij een schaal bare scenario zijn er enkele uitdagingen die kunnen worden opgelost met de juiste configuraties. In deze sectie worden de configuraties voor enkele veelvoorkomende uitvoer beschreven en worden voor beelden geboden om de opname snelheid van 1K, 5K en 10K-gebeurtenissen per seconde te ondervangen.

De volgende opmerkingen gebruiken een Stream Analytics-taak met stateless (passthrough) query, een eenvoudige Java script-UDF die schrijft naar Event hub, Azure SQL DB of Cosmos DB.

#### <a name="event-hub"></a>Event Hub

|Opname frequentie (gebeurtenissen per seconde) | Streaming-eenheden | Uitvoer resources  |
|--------|---------|---------|
| 1 K     |    1    |  2 DI   |
| 5 K     |    6    |  6 DI   |
| 10 K    |    12   |  10 DI  |

De [Event hub](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-eventhubs) -oplossing schaalt lineair in termen van streaming-eenheden (su) en door Voer, waardoor het de meest efficiënte en beste manier is om gegevens uit stream Analytics te analyseren en te streamen. Taken kunnen tot 192 SU worden geschaald, wat ongeveer vertaalt om tot 200 MB/s of 19.000.000.000.000 gebeurtenissen per dag te verwerken.

#### <a name="azure-sql"></a>Azure SQL
|Opname frequentie (gebeurtenissen per seconde) | Streaming-eenheden | Uitvoer resources  |
|---------|------|-------|
|    1 K   |   3  |  S3   |
|    5 K   |   18 |  P4   |
|    10 K  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-azuresql)  biedt ondersteuning voor het parallel schrijven, het overnemen van partitionering, maar is niet standaard ingeschakeld. Het inschakelen van partitionering, samen met een volledig parallelle query, kan echter niet voldoende zijn voor hogere door voer. Het schrijven van SQL-door Voer is aanzienlijk afhankelijk van uw database configuratie en tabel schema. Het artikel over [SQL-uitvoer prestaties](./stream-analytics-sql-output-perf.md) bevat meer details over de para meters die uw schrijf doorvoer kunnen maximaliseren. Zoals vermeld in de [Azure stream Analytics uitvoer naar Azure SQL database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) artikel, wordt deze oplossing niet lineair geschaald als een volledig parallelle pijp lijn van meer dan 8 partities en moet deze mogelijk opnieuw worden gepartitioneerd vóór SQL-uitvoer [(zie)](/stream-analytics-query/into-azure-stream-analytics#into-shard-count). Premium-Sku's zijn vereist om te voorzien in hoge i/o-tarieven, samen met de overhead van logboek back-ups die om de paar minuten worden uitgevoerd.

#### <a name="cosmos-db"></a>Cosmos DB
|Opname frequentie (gebeurtenissen per seconde) | Streaming-eenheden | Uitvoer resources  |
|-------|-------|---------|
|  1 K   |  3    | 20.000 RU  |
|  5 K   |  24   | 60K RU  |
|  10 K  |  48   | 120K RU |

[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-cosmosdb) uitvoer van stream Analytics is bijgewerkt voor het gebruik van systeem eigen integratie onder [compatibiliteits niveau 1,2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Compatibiliteits niveau 1,2 maakt aanzienlijk hogere door Voer en vermindert het gebruik van RU in vergelijking met 1,1. Dit is het standaard compatibiliteits niveau voor nieuwe taken. De oplossing maakt gebruik van CosmosDB-containers die zijn gepartitioneerd op/deviceId en de rest van de oplossing is identiek geconfigureerd.

Alle [streaming bij Azure-voor beelden van gegevens stromen](https://github.com/Azure-Samples/streaming-at-scale) gebruiken een event hub als invoer die wordt ingevoerd door test-clients te simuleren. Elke invoer gebeurtenis is een 1 KB JSON-document, waarmee geconfigureerde opname tarieven worden omgezet in doorvoer tarieven (1 MB/s, 5 MB/s en 10 MB/s). Gebeurtenissen simuleren een IoT-apparaat dat de volgende JSON-gegevens verzendt (in een Inge kort formulier) voor Maxi maal 1K apparaten:

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> De configuraties kunnen worden gewijzigd als gevolg van de verschillende onderdelen die in de oplossing worden gebruikt. Voor een nauw keuriger schatting past u de voor beelden aan uw scenario aan.

### <a name="identifying-bottlenecks"></a>Knel punten identificeren

Gebruik het deel venster metrieken in uw Azure Stream Analytics-taak om knel punten in uw pijp lijn te identificeren. Bekijk **invoer-en uitvoer gebeurtenissen** voor door Voer en [' watermerk vertraging '](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) of achterstands **gebeurtenissen** om te zien of de taak de invoer snelheid bijhoudt. Voor metrische gegevens van Event hub zoekt u naar **vertraagde aanvragen** en past u de drempel waarden dienovereenkomstig aan. Bekijk voor Cosmos DB metrische gegevens het **maximum aantal gebruikte ru/s per partitie sleutel bereik** onder door Voer om ervoor te zorgen dat de partitie sleutel bereik op gelijke wijze worden verbruikt. Bewaak **logboek-io** en **CPU** voor Azure SQL DB.

## <a name="get-help"></a>Help opvragen

Probeer voor meer hulp onze [micro soft Q&een vraag pagina voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/