---
title: Azure Stream Analytics taken omhoog en omlaag schalen
description: In dit artikel wordt beschreven hoe u een Stream Analytics taak kunt schalen door invoer gegevens te partitioneren, de query af te stemmen en de eenheid voor het streamen van taken in te stellen.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: e3d4fd6b6b83681284278d10409a1c16394db31f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018680"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Een Azure Stream Analytics taak schalen om de door voer te verhogen
In dit artikel wordt beschreven hoe u een Stream Analytics query kunt afstemmen om de door Voer voor streaming Analytics-taken te verhogen. U kunt de volgende hand leiding gebruiken om uw taak te schalen om een hogere belasting te verwerken en te profiteren van meer systeem bronnen (zoals meer band breedte, meer CPU-bronnen, meer geheugen).
Als vereiste moet u mogelijk de volgende artikelen lezen:
-   [Streaming-eenheden begrijpen en aanpassen](stream-analytics-streaming-unit-consumption.md)
-   [Kan worden opgestart-taken maken](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Voor beeld 1: uw query is inherent volledig kan worden opgestart over de verschillende invoer partities
Als uw query volledig kan worden opgestart over alle invoer partities beschikt, kunt u de volgende stappen volgen:
1.  Ontwerp uw query zodanig dat deze ongebruik maakt van een **partitie per** sleutel woord. Zie de sectie verlegen parallelle taken [op deze pagina voor](stream-analytics-parallelization.md)meer informatie.
2.  Afhankelijk van de uitvoer typen die in uw query worden gebruikt, is het mogelijk dat een bepaalde uitvoer niet kan worden opgestart is of dat de verdere configuratie nog te maken heeft. Bijvoorbeeld, de Power bi-uitvoer is niet kan worden opgestart. Uitvoer wordt altijd samengevoegd voordat deze naar de uitvoer sink wordt verzonden. Blobs, Tables, ADLS, Service Bus en Azure function worden automatisch geparallelleerd. SQL en Azure Synapse Analytics-uitvoer hebben een optie voor parallel Lise ring. Voor de Event hub moet de configuratie van PartitionKey zijn ingesteld op overeenkomen met het veld **Partition by** (meestal PartitionId). Voor Event hub moet u ook extra aandacht best Eden aan het aantal partities voor alle invoer en alle uitvoer om te voor komen dat er kruisen tussen partities worden uitgevoerd. 
3.  Voer uw query uit met **6 su** (dit is de volledige capaciteit van een knoop punt met één computer) om de maximale Haal bare door voer te meten. Als u **Group by** gebruikt, meet u hoeveel groepen (kardinaliteit) de taak kan verwerken. Algemene symptomen van de systeem resource limieten voor taak beheer zijn het volgende.
    - De metrische gegevens over het gebruik van% zijn meer dan 80%. Dit geeft aan dat het geheugen gebruik hoog is. De factoren die bijdragen aan de toename van deze metriek worden [hier](stream-analytics-streaming-unit-consumption.md)beschreven. 
    -   Uitvoer tempel ligt achter met betrekking tot de klok tijd van de wand. Afhankelijk van uw query logica, kan het uitvoer tijds tempel een logische offset hebben van de klok tijd van de wand. Ze moeten echter op ongeveer dezelfde snelheid worden uitgevoerd. Als het tijds tempel van de uitvoer verder en verder ligt, is het een indicatie dat het systeem overwerkt. Dit kan een resultaat zijn van de stroomafwaartse beperking van het uitvoer filter of een hoog CPU-gebruik. We bieden op dit moment geen CPU-gebruik, dus het kan lastig zijn om de twee te onderscheiden.
        - Als het probleem wordt veroorzaakt door limieten voor sinks, moet u mogelijk het aantal uitvoer partities (en ook de invoer partities om de taak volledig te kan worden opgestart) verg Roten of de hoeveelheid resources van de Sink verhogen (bijvoorbeeld aantal aanvraag eenheden voor CosmosDB).
    - In taak diagram is er een gebeurtenis metriek per partitie achterstand voor elke invoer. Als de gebeurtenis metriek van de achterstand blijft toenemen, is het ook een indicatie dat de systeem bron is beperkt (als gevolg van beperking van de uitvoer-sink of een hoge CPU).
4.  Zodra u hebt bepaald hoe een 6 SU-taak kan bereiken, kunt u lineair de verwerkings capaciteit van de taak extrapoleren wanneer u meer SUs toevoegt, ervan uitgaande dat u geen gegevens scheef hebt die een bepaalde partitie ' hot ' maakt.

> [!NOTE]
> Kies het juiste aantal streaming-eenheden: omdat Stream Analytics een verwerkings knooppunt maakt voor elke 6 SU toegevoegd, is het het beste om het aantal knoop punten een deler van het aantal invoer partities te maken, zodat de partities gelijkmatig over de knoop punten kunnen worden verdeeld.
> U hebt bijvoorbeeld gemeten dat uw 6 SU-taak 4 MB/s verwerkings snelheden kan bezorgen en dat het aantal invoer partities 4 is. U kunt ervoor kiezen om uw taak uit te voeren met 12 SU voor een verwerkings frequentie van ongeveer 8 MB/s, of 24 SU om 16 MB/s te krijgen. U kunt vervolgens bepalen wanneer u het SU-nummer voor de taak wilt verg Roten naar welke waarde, als een functie van uw invoer frequentie.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Voor beeld 2: als uw query niet-verlegen parallel is.
Als uw query niet verlegen parallel is, kunt u de volgende stappen volgen.
1.  Begin met een query zonder **partitie door** eerst te voor komen dat u de complexiteit partitioneert en voer de query uit met 6 su om de maximale belasting te meten als in het [geval 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Als u in de loop van de door Voer een verwachte belasting kunt krijgen, bent u klaar. U kunt er ook voor kiezen om de taak die wordt uitgevoerd op 3 SU en 1 SU te meten om het minimale aantal SU te bepalen dat geschikt is voor uw scenario.
3.  Als de gewenste door Voer niet kan worden bereikt, probeert u de query in meerdere stappen indien mogelijk te verstoren, als deze nog niet meerdere stappen bevat, en u kunt Maxi maal 6 SU toewijzen voor elke stap in de query. Als u bijvoorbeeld drie stappen hebt, wijst u 18 SU toe in de optie schalen.
4.  Wanneer een dergelijke taak wordt uitgevoerd, plaatst Stream Analytics elke stap op een eigen knoop punt met toegewezen 6 SU-resources. 
5.  Als u nog steeds het laad doel hebt bereikt, kunt u proberen om de partitie te gebruiken **door** te beginnen bij de stappen in de invoer. Voor **Group by** -operator die niet op natuurlijke wijze kan worden gepartitioneerd, kunt u het lokale/globale aggregatie patroon gebruiken voor het uitvoeren van een gepartitioneerde **groep door** een niet-GEpartitioneerde **groep door** te volgen. Bijvoorbeeld, als u wilt tellen hoeveel auto's elke drie minuten elk uur duren, en het volume van de gegevens niet meer kan worden verwerkt door 6 SU.

Query:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
In de bovenstaande query telt u auto's per gelaagde stand per partitie en voegt u vervolgens het aantal in alle partities samen toe.

Zodra een partitie is gepartitioneerd, wijst u voor elke partitie van de stap Maxi maal 6 SU toe, elke partitie met 6 SU is het maximum, zodat elke partitie kan worden geplaatst op een eigen verwerkings knooppunt.

> [!Note]
> Als uw query niet kan worden gepartitioneerd, kunt u de door Voer niet altijd verbeteren door extra SU toe te voegen in een query met meerdere stappen. Een van de manieren om prestaties te verkrijgen is om het volume te verminderen van de eerste stappen met behulp van lokaal/globaal aggregatie patroon, zoals hierboven beschreven in stap 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Case 3: u voert veel onafhankelijke query's uit in een taak.
Voor bepaalde ISV-use-cases, waarbij het rendabeler is om gegevens van meerdere tenants in één taak te verwerken, met behulp van afzonderlijke invoer en uitvoer voor elke Tenant, kunt u uiteindelijk enkele enkele (bijvoorbeeld 20) onafhankelijke query's uitvoeren in één taak. De veronderstelling is dat de belasting van deze subquery relatief klein is. In dit geval kunt u de volgende stappen volgen.
1.  Gebruik in dit geval geen **partitie door** in de query
2.  Verminder het aantal invoer partities tot de laagst mogelijke waarde 2 als u Event hub gebruikt.
3.  Voer de query uit met 6 SU. Met een verwachte belasting voor elke subquery, voegt u zoveel mogelijk subquery's toe, totdat de taak limieten voor systeem bronnen wordt bereikt. Raadpleeg [Case 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) voor de symptomen wanneer dit gebeurt.
4.  Zodra u de hierboven gemeten subquery-limiet hebt bereikt, begint u met het toevoegen van de subquery aan een nieuwe taak. Het aantal taken om uit te voeren als een functie van het aantal onafhankelijke query's moet tamelijk lineair zijn, ervan uitgaande dat u geen taak verschil hebt. U kunt vervolgens een prognose maken van het aantal 6 SU-taken dat u moet uitvoeren als een functie van het aantal tenants dat u wilt gebruiken.
5.  Wanneer u referentie gegevens samenvoegt met dergelijke query's, moet u de invoer samen voegen voordat u met dezelfde referentie gegevens samenvoegt. Daarna kunt u de gebeurtenissen zo nodig opsplitsen. Anders houdt elke samen voeging met referentie gegevens een kopie van referentie gegevens in het geheugen, waardoor het geheugen gebruik waarschijnlijk onnodig wordt.

> [!Note] 
> Hoeveel tenants wilt u in elke taak plaatsen?
> Dit query patroon heeft vaak een groot aantal subquery's en resulteert in een zeer grote en complexe topologie. De controller van de taak kan een dergelijke grote topologie mogelijk niet verwerken. Blijf onder 40-tenants voor 1 SU-taak en 60-tenants voor 3 SU-en 6 SU-taken. Wanneer u de capaciteit van de controller overschrijdt, wordt de taak niet gestart.



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