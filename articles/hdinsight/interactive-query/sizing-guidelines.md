---
title: Aanpassings gids voor interactieve query clusters in azure HDInsight
description: Aanpassings gids voor interactieve Query's in azure HDInsight
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a7baa9340a1f0a99b94bfcbe535c73d0b502e2a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933067"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Aanpassings gids voor interactieve query clusters in azure HDInsight

In dit document wordt de grootte van het HDInsight Interactive query-cluster (LLAP) voor een typische werk belasting beschreven om redelijke prestaties te behalen. De aanbevelingen die in dit document worden gegeven, zijn algemene en specifieke workloads hebben mogelijk specifieke afstemming nodig.

## <a name="default-vm-types-for-interactive-query"></a>Standaard VM-typen voor interactieve Query's

| Knooppunt type | Exemplaar | Grootte |
|---|---|---|
| Head | D13 v2 | 8 VCPU'S, 56 GB RAM, 400 GB SSD |
| Werk | D14 v2 | 16 VCPU'S, 112 GB RAM, 800 GB SSD |
| ZooKeeper | A4 v2 | 4 VCPU'S, 8 GB RAM, 40 GB SSD |

## <a name="recommended-configurations"></a>Aanbevolen configuraties

De aanbevolen configuratie waarden zijn gebaseerd op het werk knooppunt van het D14 v2-type.

| Sleutel | Waarde | Beschrijving |
|---|---|---|
| garens. nodemanager. resource. geheugen-MB | 102400 (MB) | Totale hoeveelheid geheugen in MB, voor alle GARENs in een knoop punt. |
| garens. scheduler. maximum-toewijzing-MB | 102400 (MB) | De maximale toewijzing voor elke container aanvraag op de RM, in MB. Geheugen aanvragen die hoger zijn dan deze waarde, worden niet van kracht. |
| garens. scheduler. maximum-toewijzing-vcores | 12 |Het maximum aantal CPU-kernen voor elke container aanvraag in de Resource Manager. Aanvragen die hoger zijn dan deze waarde, worden niet van kracht. |
| garens. scheduler. capacity. root. llap. capacity | 90% | Toewijzing van garen capaciteit voor LLAP-wachtrij.  |
| Hive. server2. TEZ. Sessions. per. default. Queue | number_of_worker_nodes |Het aantal sessies voor elke wachtrij met de naam in de component. server2. TEZ. default. queues. Dit aantal komt overeen met het aantal query-coördinatoren (TEZ AMs). |
| TEZ. am. resource. Memory. MB | 4096 (MB) | De hoeveelheid geheugen in MB die moet worden gebruikt door de TEZ AppMaster. |
| component. TEZ. container. size | 4096 (MB) | Opgegeven grootte van de TEZ-container in MB. |
| hive.llap.daemon.num.executors | 12 | Aantal uitvoerende webuitvoeringen per LLAP-daemon. |
| Hive. llap. io. thread pool. grootte | 12 | De grootte van de thread groep voor uitvoerender. |
| Hive. llap. daemon. garens. container. MB | 86016 (MB) | Totaal geheugen in MB dat door afzonderlijke LLAP-daemons (geheugen per daemon) wordt gebruikt.|
| Hive. llap. io. Memory. size | 409600 (MB) | Cache grootte in MB per LLAP-daemon die SSD-cache aanbiedt, is ingeschakeld. |
| component. auto. Convert. samen. noconditionaltask. size | 2048 (MB) | geheugen grootte in MB to do koppeling toevoegen. |

## <a name="llap-daemon-size-estimations"></a>Schattingen van LLAP-daemon grootte  

### <a name="yarnnodemanagerresourcememory-mb"></a>garens. nodemanager. resource. geheugen-MB

Deze waarde wijst op een maximum hoeveelheid geheugen in MB die wordt gebruikt door de GARENs op elk knoop punt. Hiermee wordt aangegeven hoeveel geheugen GARENs op dit knoop punt kunnen worden gebruikt, dus deze waarde moet lager zijn dan het totale geheugen op het knoop punt.

Stel deze waarde in op [totaal fysiek geheugen op het knoop punt] – [geheugen voor OS + andere services].

U kunt deze waarde het beste instellen op ~ 90% van het beschik bare RAM-geheugen. Voor D14 v2 is de aanbevolen waarde **102400 MB**.

### <a name="yarnschedulermaximum-allocation-mb"></a>garens. scheduler. maximum-toewijzing-MB

Met deze waarde wordt de maximale toewijzing aangegeven voor elke container aanvraag in de Resource Manager, in MB. Geheugen aanvragen die hoger zijn dan de opgegeven waarde, worden niet van kracht. De Resource Manager kan alleen geheugen toewijzen aan containers in stappen van `yarn.scheduler.minimum-allocation-mb` en mag niet groter zijn dan de grootte die is opgegeven door `yarn.scheduler.maximum-allocation-mb` . Deze waarde mag niet groter zijn dan het totale gegeven geheugen van het knoop punt, dat wordt opgegeven door `yarn.nodemanager.resource.memory-mb` .

Voor D14 v2-worker-knoop punten is de aanbevolen waarde **102400 MB**

### <a name="yarnschedulermaximum-allocation-vcores"></a>garens. scheduler. maximum-toewijzing-vcores

Deze configuratie geeft het maximum aantal virtuele CPU-kernen aan voor elke container aanvraag in de Resource Manager. Het aanvragen van een hogere waarde is niet van kracht. Deze configuratie is een algemene eigenschap van de garen planner. Voor de LLAP-daemon-container kan deze waarde worden ingesteld op 75% van de totale beschik bare virtuele kernen (VCORES). De resterende 25% moet worden gereserveerd voor NodeManager, DataNode en andere services die worden uitgevoerd op de worker-knoop punten.  

Voor D14 v2-worker-knoop punten zijn er 16 VCORES en 75% van 16 VCORES kan worden opgegeven. Daarom is de aanbevolen waarde voor de LLAP-daemon-container **12**.

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>Hive. server2. TEZ. Sessions. per. default. Queue

Deze configuratie waarde bepaalt het aantal TEZ-sessies dat parallel moet worden gestart voor elk van de wacht rijen die door worden opgegeven `hive.server2.tez.default.queues` . De waarde komt overeen met het aantal TEZ-AMs (query-coördinatoren). Het is raadzaam hetzelfde te zijn als het aantal worker-knoop punten dat één TEZ per knoop punt heeft. Het aantal TEZ-AMs kan hoger zijn dan het aantal LLAP-daemon-knoop punten. De belangrijkste verantwoordelijkheid is het coördineren van de uitvoering van de query en het toewijzen van query plan fragmenten aan bijbehorende LLAP-daemons voor uitvoering. Het wordt aanbevolen om deze te gebruiken als meerdere van een aantal LLAP-daemon-knoop punten om een hogere door voer te krijgen.  

Het standaard HDInsight-cluster bevat vier LLAP-daemons die worden uitgevoerd op vier worker-knoop punten, dus de aanbevolen waarde is **4**.  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>TEZ. am. resource. Memory. MB, component. TEZ. container. size

`tez.am.resource.memory.mb` Hiermee wordt de grootte van de toepassings Master van de TEZ gedefinieerd.  
De aanbevolen waarde is **4096 MB**.

`hive.tez.container.size` Hiermee definieert u de hoeveelheid geheugen die is opgegeven voor de TEZ-container. Deze waarde moet worden ingesteld tussen de minimale container grootte ( `yarn.scheduler.minimum-allocation-mb` ) en de maximale container grootte () van de garens `yarn.scheduler.maximum-allocation-mb` .  
U kunt het beste instellen op **4096 MB**.  

Een algemene regel is om deze lager te houden dan de hoeveelheid geheugen per processor, waarbij één processor per container wordt overwogen. `Rreserve` geheugen voor het aantal TEZ AMs op een knoop punt voordat het geheugen voor LLAP-daemon wordt aangeboden. Als u bijvoorbeeld twee TEZ AMs (elk 4 GB per knoop punt) gebruikt, geeft u 82 GB van 90 GB voor LLAP-daemon die 8 GB reserveert voor twee TEZ AMs.

### <a name="yarnschedulercapacityrootllapcapacity"></a>garens. scheduler. capacity. root. llap. capacity

Deze waarde geeft een percentage van de capaciteit aan dat is opgegeven voor de LLAP-wachtrij. Het HDInsights Interactive query-cluster geeft 90% van de totale capaciteit voor LLAP-wachtrij en de resterende 10% is ingesteld op de standaard wachtrij voor andere container toewijzingen.  
Voor D14v2 worker-knoop punten is de aanbevolen waarde **90** voor LLAP-wachtrij.

### <a name="hivellapdaemonyarncontainermb"></a>Hive. llap. daemon. garens. container. MB

De totale geheugen grootte voor LLAP-daemon is afhankelijk van de volgende onderdelen:

* Configuratie van de grootte van de garen container ( `yarn.scheduler.maximum-allocation-mb` , `yarn.scheduler.maximum-allocation-mb` , `yarn.nodemanager.resource.memory-mb` )

* Heap-geheugen dat wordt gebruikt door de (xmx)

    De hoeveelheid RAM-geheugen dat beschikbaar is nadat de vrije ruimte is bereikt.  
    Voor D14 v2, HDI 4,0-deze waarde is (86 GB-6 GB) = 80 GB  
    Voor D14 v2, HDI 3,6-deze waarde is (84 GB-6 GB) = 78 GB

* Off-heap in het geheugen cache per daemon (Hive. llap. io. Memory. size)

* Ruimte

    Het is een deel van een off-heap geheugen dat wordt gebruikt voor de overhead van Java-vm's (meta Space, threads stack, GC-gegevens structuren, enzovoort). Dit gedeelte wordt waargenomen tot ongeveer 6% van de Heap-grootte (xmx). Om aan de veiligste zijde te zijn, kan het worden berekend als 6% van de totale geheugen grootte van LLAP daemon. Omdat de SSD-cache is ingeschakeld, kan de LLAP-daemon alle beschik bare ruimte in het geheugen gebruiken om alleen voor de heap te worden gebruikt.  
    Voor D14 v2 is de aanbevolen waarde ceil (86 GB x 0,06) ~ = **6 GB**.  

Geheugen per daemon = [cache grootte in het geheugen] + [Heap-grootte] + [ruimte].

Dit kan als volgt worden berekend:

TEZ AM-geheugen per knoop punt = [(aantal TEZ AMs/aantal LLAP daemon-knoop punten) * TEZ AM size].
Grootte van LLAP-daemon-container = [90% van garen Maxi maal container geheugen]-[TEZ-geheugen per knoop punt].

Voor D14 v2 worker-knoop punt HDI 4,0-de aanbevolen waarde is (90-(1/1 * 4 GB)) = **86 GB**.
(HDI 3,6, aanbevolen waarde is **84 GB** , omdat u de reserve ring moet reserveren ~ 2 GB voor de schuif regelaar.)  

### <a name="hivellapiomemorysize"></a>Hive. llap. io. Memory. size

Deze configuratie is de hoeveelheid geheugen die beschikbaar is als cache voor LLAP-daemon. De LLAP-daemons kunnen SSD als cache gebruiken. Als deze optie is ingesteld op `hive.llap.io.allocator.mmap` True, wordt SSD-caching ingeschakeld. De D14 v2 wordt geleverd met ~ 800 GB SSD en de cache functie SSD is standaard ingeschakeld voor het interactieve query cluster (LLAP). Het is geconfigureerd voor het gebruik van 50% van de SSD-ruimte voor een off-heap-cache.

Voor D14 v2 is de aanbevolen waarde **409600 MB**.  

Voor andere virtuele machines zonder SSD-caching is ingeschakeld, is het nuttig om een deel van het beschik bare RAM-geheugen voor LLAP cache te geven voor betere prestaties. U kunt als volgt de totale geheugen grootte voor LLAP-daemon aanpassen:  

Totale LLAP-daemon-geheugen = [LLAP cache grootte] + [Heap-grootte] + [ruimte].

Het is raadzaam de cache grootte en de Heap-grootte aan te passen die het meest geschikt is voor uw werk belasting.  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.executors

Deze configuratie bepaalt het aantal uitvoerender dat taken parallel kan uitvoeren per LLAP-daemon. Deze waarde is een saldo van het aantal beschik bare VCORES, de hoeveelheid geheugen die per uitvoerder wordt gegeven en het totale beschik bare geheugen per LLAP-daemon. Normaal gesp roken moeten we deze waarde zo dicht mogelijk bij het aantal kernen bevinden.

Voor D14 v2 zijn er 16 VCORES beschikbaar, maar niet alle VCORES kunnen worden opgegeven. De worker-knoop punten voeren ook andere services uit, zoals NodeManager, DataNode en metrische monitors, die een deel van de beschik bare VCORES nodig hebben. Deze waarde kan Maxi maal 75% van het totale aantal beschik bare VCORES op dat knoop punt worden geconfigureerd.

Voor D14 v2 is de aanbevolen waarde (. 75 X 16) = **12**

U kunt het beste een reserve ring van Maxi maal 6 GB opslag ruimte per uitvoerder maken. Pas het aantal uitvoerendeers aan op basis van de beschik bare LLAP-daemon-grootte en het aantal beschik bare VCORES per knoop punt.  

### <a name="hivellapiothreadpoolsize"></a>Hive. llap. io. thread pool. grootte

Met deze waarde wordt de thread pool grootte voor uitvoerende toepassingen opgegeven. Omdat de uitvoeringen van de toepassing zijn hersteld, is deze hetzelfde als het aantal uitvoerender per LLAP-daemon.

Voor D14 v2 kunt u deze waarde het beste instellen op **12**.

Deze configuratie kan niet groter zijn dan de `yarn.nodemanager.resource.cpu-vcores` waarde.

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>component. auto. Convert. samen. noconditionaltask. size

Zorg ervoor dat u hebt `hive.auto.convert.join.noconditionaltask` ingeschakeld om deze para meter van kracht te laten worden. Met deze configuratie kan de gebruiker de grootte opgeven van de tabellen die in het geheugen passen om de toewijzings koppeling uit te voeren. Als de som van de grootte van n-1 van de `tables/partitions` voor n-eenrichtings-deelname kleiner is dan de geconfigureerde waarde, wordt de kaart koppeling gekozen. De geheugen grootte van de LLAP-uitvoerder moet worden gebruikt voor het berekenen van de drempel waarde voor automatisch converteren naar kaart koppelen.

Voor D14 v2 kunt u deze waarde het beste instellen op **2048 MB**.

Het is raadzaam om deze waarde aan te passen die geschikt is voor uw werk belasting, omdat het instellen van deze waarde te laag mogelijk geen gebruik maakt van de functie voor het converteren van een Als u het te hoog instelt, kan dit leiden tot GC-onderbrekingen. Dit kan een negatieve invloed hebben op de prestaties van query's.

## <a name="next-steps"></a>Volgende stappen

* [Gateway richtlijnen](gateway-best-practices.md)
* [Fijnafstelling van mysterie Apache TEZ-geheugen-stap voor stap](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [Geheugen grootte van kaart koppelen voor LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP-een architectuur overzicht van één pagina](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [LLAP grondige kennis van Hive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
