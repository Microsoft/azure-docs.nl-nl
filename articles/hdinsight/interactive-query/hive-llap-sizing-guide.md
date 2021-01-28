---
title: Aanpassings gids voor LLAP (HDInsight Interactive query cluster)
description: LLAP-formaat gids
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: 7df75077785c66215008e045ef0b1e451ba29f57
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931104"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Azure HDInsight Interactive query-cluster (Hive LLAP) formaat gids

In dit document wordt de grootte van het HDInsight Interactive query-cluster (Hive LLAP-cluster) beschreven voor een typische werk belasting om redelijke prestaties te behalen. Houd er rekening mee dat de aanbevelingen die in dit document worden gegeven algemene richt lijnen en specifieke werk belastingen mogelijk specifieke afstemming nodig hebben.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Standaard VM-typen van Azure voor HDInsight Interactive query cluster (LLAP)**

| Knooppunt type      | Exemplaar | Grootte     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vcpu's, 56 GB RAM, 400 GB SSD   |
| Werk   | **D14 v2**        | **16 vcpu's, 112 GB RAM, 800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 vcpu's, 8 GB RAM, 40 GB SSD       |

**_Opmerking: alle aanbevolen configuratie waarden zijn gebaseerd op het D14 v2-type werk knooppunt_* _  

### <a name="_configuration"></a>_ *Configuratie:**    
| Configuratiesleutel      | Aanbevolen waarde  | Beschrijving |
| :---        |    :----:   | :---     |
| garens. nodemanager. resource. geheugen-MB | 102400 (MB) | Totale hoeveelheid geheugen in MB, voor alle GARENs in een knoop punt | 
| garens. scheduler. maximum-toewijzing-MB | 102400 (MB) | De maximale toewijzing voor elke container aanvraag op de RM, in MB. Geheugen aanvragen die hoger zijn dan deze waarde, worden niet van kracht |
| garens. scheduler. maximum-toewijzing-vcores | 12 |Het maximum aantal CPU-kernen voor elke container aanvraag in de Resource Manager. Aanvragen die hoger zijn dan deze waarde, worden niet van kracht. |
| garens. nodemanager. resource. CPU-vcores | 12 | Aantal CPU-kernen per NodeManager dat voor containers kan worden toegewezen. |
| garens. scheduler. capacity. root. llap. capacity | 85 (%) | Toewijzing van garen capaciteit voor llap-wachtrij  |
| TEZ. am. resource. Memory. MB | 4096 (MB) | De hoeveelheid geheugen in MB die moet worden gebruikt door de TEZ AppMaster |
| Hive. server2. TEZ. Sessions. per. default. Queue | <number_of_worker_nodes> |Het aantal sessies voor elke wachtrij met de naam in de component. server2. TEZ. default. queues. Dit aantal komt overeen met het aantal query-coördinatoren (TEZ AMs) |
| component. TEZ. container. size | 4096 (MB) | Opgegeven TEZ-container grootte in MB |
| hive.llap.daemon.num.executors | 19 | Aantal uitvoerender per LLAP-daemon | 
| Hive. llap. io. thread pool. grootte | 19 | Grootte van thread pool voor uitvoerendeers |
| Hive. llap. daemon. garens. container. MB | 81920 (MB) | Totaal geheugen in MB dat wordt gebruikt door afzonderlijke LLAP-daemons (geheugen per daemon)
| Hive. llap. io. Memory. size | 242688 (MB) | Cache grootte in MB per LLAP-daemon van SSD-cache is ingeschakeld |
| component. auto. Convert. samen. noconditionaltask. size | 2048 (MB) | geheugen grootte in MB to do koppelings koppeling |

### <a name="llap-architecturecomponents"></a>**LLAP-architectuur/-onderdelen:**  

![' LLAP-architectuur/-onderdelen '](./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png "LLAP-architectuur/-onderdelen")

### <a name="llap-daemon-size-estimations"></a>**Schattingen voor de grootte van LLAP-daemon:** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. de toewijzing van het totale aantal GARENs in een knoop punt bepalen**    
Configuratie: **_garens. nodemanager. resource. geheugen-MB_* _  

Deze waarde wijst op een maximum hoeveelheid geheugen in MB die kan worden gebruikt door de garen containers op elk knoop punt. De opgegeven waarde moet kleiner zijn dan de totale hoeveelheid fysiek geheugen op het knoop punt.   
Totaal geheugen voor alle GARENs in een knoop punt = (totaal fysiek geheugen – geheugen voor OS + andere services)  
Stel deze waarde in op ~ 90% van de beschik bare RAM-grootte.  
Voor D14 v2 is de aanbevolen waarde _ * 102400 MB * *. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. het bepalen van de maximale hoeveelheid geheugen per garen-container aanvraag**  
Configuratie: **_garens. scheduler. maximum-toewijzing-MB_* _

Met deze waarde wordt de maximale toewijzing aangegeven voor elke container aanvraag in de Resource Manager, in MB. Geheugen aanvragen die hoger zijn dan de opgegeven waarde, worden niet van kracht. De Resource Manager kan geheugen toewijzen aan containers in stappen van _yarn. scheduler. minimum-toewijzing-MB * en mag niet groter zijn dan de grootte die is opgegeven door de *garens. scheduler-MB. Maxi maal-toewijzings geheugen*. De opgegeven waarde mag niet groter zijn dan het totale gegeven geheugen voor alle containers op het knoop punt dat is opgegeven door *garens. nodemanager. resource. geheugen-MB*.    
Voor D14 v2-worker-knoop punten is de aanbevolen waarde **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. het bepalen van de maximale hoeveelheid vcores per garen-container aanvraag**  
Configuratie: **_garens. scheduler. maximum-Allocation-vcores_* _  

Deze waarde geeft het maximum aantal virtuele CPU-kernen aan voor elke container aanvraag in de Resource Manager. Als u een hoger aantal vcores wilt aanvragen, wordt deze waarde niet van kracht. Het is een wereld wijde eigenschap van de garen planner. Voor de LLAP-daemon-container kan deze waarde worden ingesteld op 75% van de totale beschik bare vcores. De resterende 25% moet worden gereserveerd voor NodeManager, DataNode en andere services die worden uitgevoerd op de worker-knoop punten.  
Er zijn 16 vcores op D14 v2-Vm's en 75% van het totale aantal 16 vcores kunnen worden gebruikt door de LLAP daemon-container.  
Voor D14 v2 is de aanbevolen waarde _ * 12 * *.  

#### <a name="4-number-of-concurrent-queries"></a>**4. aantal gelijktijdige query's**  
Configuratie: **_Hive. server2. TEZ. Sessions. per. default. Queue_* _

Deze configuratie waarde bepaalt het aantal TEZ-sessies dat parallel kan worden gestart. Deze TEZ-sessies worden gestart voor elk van de wacht rijen die zijn opgegeven met de component. server2. TEZ. default. queues. Dit komt overeen met het aantal TEZ-AMs (query-coördinatoren). Het is raadzaam hetzelfde te zijn als het aantal worker-knoop punten. Het aantal TEZ-AMs kan hoger zijn dan het aantal LLAP-daemon-knoop punten. De primaire verantwoordelijkheid van TEZ is het coördineren van de uitvoering van de query en het toewijzen van query plan fragmenten aan bijbehorende LLAP-daemons voor uitvoering. Bewaar deze waarde als een veelvoud van een aantal LLAP-daemon-knoop punten om een hogere door voer te krijgen.  

Het standaard HDInsight-cluster bevat vier LLAP-daemons die worden uitgevoerd op vier worker-knoop punten, dus de aanbevolen waarde is _ * 4 * *.  

**Schuif regelaar van Ambari-gebruikers interface voor Hive-configuratie variabele `hive.server2.tez.sessions.per.default.queue` :**

![' LLAP maximum aantal gelijktijdige query's '](./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png "Maximum aantal gelijktijdige query's LLAP")

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. TEZ-container en TEZ-toepassings hoofd grootte**    
Configuratie: **_TEZ. am. resource. Memory. MB, Hive. TEZ. container. grootte_* _  

_tez. am. resource. Memory. MB *: definieert de grootte van de toepassings Master van de TEZ.  
De aanbevolen waarde is **4096 MB**.
   
*Hive. TEZ. container. size* : Hiermee definieert u de hoeveelheid geheugen die is opgegeven voor de TEZ-container. Deze waarde moet worden ingesteld tussen de minimale container grootte van het garen (*garens. scheduler. minimum-toewijzings MB*) en de maximale container grootte van de garens (*garens. scheduler. maximum-toewijzing-MB*). De LLAP daemon-uitvoeringen gebruiken deze waarde voor het beperken van het geheugen gebruik per uitvoerder.  
De aanbevolen waarde is **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. toewijzing van LLAP-wachtrij capaciteit**   
Configuratie: **_garens. scheduler. capacity. root. llap. capacity_* _  

Met deze waarde wordt een percentage van de capaciteit aangegeven dat is opgegeven voor de llap-wachtrij. De capaciteits toewijzingen kunnen verschillende waarden voor verschillende werk belastingen hebben, afhankelijk van de configuratie van de garen-wacht rijen. Als uw werk belasting alleen-lezen is, stelt u deze in op Maxi maal 90% van de capaciteit zou moeten werken. Als uw werk belasting echter een combi natie is van update/delete/merge-bewerkingen met behulp van beheerde tabellen, is het raadzaam om 85% van de capaciteit voor llap-wachtrij te geven. De resterende capaciteit van 15% kan worden gebruikt door andere taken, zoals compressie, enzovoort om containers toe te wijzen vanuit de standaard wachtrij. Op die manier kunnen de taken in de standaard wachtrij geen GARENs meer afnemen.    

Voor D14v2 worker-knoop punten is de aanbevolen waarde voor de llap-wachtrij _ * 85 * *.     
(Voor ReadOnly-workloads kan het worden verhoogd tot 90 als geschikt.)  

#### <a name="7-llap-daemon-container-size"></a>**7. LLAP daemon-container grootte**    
Configuratie: **_Hive. llap. daemon. garens. container. MB_* _  
   
LLAP-daemon wordt uitgevoerd als een GARENve container op elk worker-knoop punt. De totale geheugen grootte voor de LLAP-daemon-container is afhankelijk van de volgende factoren:    
_ Configuraties van de grootte van de garen container (garens. scheduler. minimum-toewijzing-MB, garens. scheduler. maximum-toewijzing-MB, garens. nodemanager. resource. geheugen-MB)
*  Aantal TEZ-AMs op een knoop punt
*  Totaal geheugen geconfigureerd voor alle containers op een knoop punt en LLAP-wachtrij capaciteit  

Het geheugen dat nodig is voor TEZ Application Masters (TEZ AM) kan als volgt worden berekend.  
TEZ AM fungeert als een query coördinator en het aantal TEZ-AMs moet worden geconfigureerd op basis van een aantal gelijktijdige query's die moeten worden geleverd. In theorie kunnen we één TEZ per worker-knoop punt beschouwen. Het is echter mogelijk dat er meer dan één TEZ-uur op een worker-knoop punt wordt weer geven. Voor het berekenen van het doel gaan we uitgaan van een uniforme distributie van TEZ AMs over alle LLAP-daemon-knoop punten/worker-knoop punten.
Het is raadzaam 4 GB geheugen per TEZ uur te hebben.  

Aantal TEZ AMS = waarde dat is opgegeven door Hive Configuration ***Hive. server2. TEZ. Sessions. default. Queue** _.  
Aantal LLAP-daemon-knoop punten = opgegeven door de env-variabele _*_num_llap_nodes_for_llap_daemons_*_ in de Ambari-gebruikers interface.  
TEZ AM container size = waarde opgegeven door TEZ config _*_TEZ. am. resource. Memory. MB_*_.  

TEZ am Memory per knoop punt =*_ (** CEIL **(** aantal TEZ AMS **/** aantal LLAP-daemon-knoop punten **)** **x** TEZ am **-** container grootte)  
Voor D14 v2 heeft de standaard configuratie vier TEZ-AMs en vier LLAP-daemon-knoop punten.  
TEZ AM-geheugen per knoop punt = (CEIL (4/4) x 4 GB) = 4 GB

Het totale beschik bare geheugen voor de LLAP-wachtrij per werk knooppunt kan als volgt worden berekend:  
Deze waarde is afhankelijk van de totale hoeveelheid geheugen die beschikbaar is voor alle GARENs op een knoop punt (*garens. nodemanager. resource. geheugen-MB*) en het percentage capaciteit dat is geconfigureerd voor de llap-wachtrij (*garens. scheduler. capacity. root. llap. capacity*).  
Totaal geheugen voor LLAP-wachtrij op worker-knoop punt = totaal geheugen beschikbaar voor alle GARENs van een knoop punt x capaciteit voor LLAP-wachtrij.  
Voor D14 v2 is deze waarde (100 GB x 0,85) = 85 GB.

De grootte van de LLAP-daemon-container wordt als volgt berekend:

**Grootte van LLAP-daemon-container = (totaal geheugen voor LLAP-wachtrij op een workernode) – (TEZ AM-geheugen per knoop punt): (grootte van container van service Master)**  
Er is slechts één service Master (toepassings Master voor LLAP-service) op het cluster die op een van de worker-knoop punten is geproduceerd. Voor het berekenings doel beschouwen we één service Master per worker-knoop punt.  
Voor D14 v2 worker-knoop punt HDI 4,0-de aanbevolen waarde is (85 GB-4 GB-1 GB)) = **80 GB**   
(HDI 3,6, aanbevolen waarde is **79 GB** , omdat u extra moet reserveren ~ 2 GB voor schuifregelaar am.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. bepalen van het aantal uitvoerender per LLAP-daemon**  
Configuratie: **_hive.llap.daemon.num.executors_* _, _*_Hive. llap. io. thread pool. size_*_

_*_hive.llap.daemon.num.executors_*_:   
Deze configuratie bepaalt het aantal uitvoerender dat taken parallel kan uitvoeren per LLAP-daemon. Deze waarde is afhankelijk van het aantal vcores, de hoeveelheid geheugen die per uitvoerder wordt gebruikt en de totale hoeveelheid geheugen die beschikbaar is voor de LLAP daemon-container.    Het aantal uitvoerende agents kan worden geabonneerd op 120% van de beschik bare vcores per worker-knoop punt. Het moet echter worden aangepast als deze niet voldoet aan de geheugen vereisten op basis van het geheugen dat nodig is voor de uitvoerder en de container grootte van de LLAP-daemon.

Elke uitvoerder is gelijk aan een TEZ-container en kan 4 GB (TEZ-container grootte) aan geheugen gebruiken. Alle uitvoerders in de LLAP-daemon delen hetzelfde heap-geheugen. Met de veronderstelling dat niet alle uitvoeringen geheugenintensieve bewerkingen tegelijk uitvoeren, kunt u 75% van de TEZ-container grootte (4 GB) per uitvoerder overwegen. Op deze manier kunt u het aantal uitvoerers verhogen door elke uitvoerder minder geheugen (bijvoorbeeld 3 GB) te geven voor een verhoogde parallelle uitvoering. Het wordt echter aangeraden deze instelling af te stemmen op uw doel-workload.

Er zijn 16 vcores op D14 v2-Vm's.
Voor D14 v2 is de aanbevolen waarde voor aantal uitvoerers (16 vcores x 120%) ~ = _ *19** op elk worker-knoop punt, met 3 GB per uitvoerder.

**_Hive. llap. io. thread pool. grootte_*_: deze waarde geeft de grootte van de thread groep voor uitvoerders. Omdat de uitvoeringen van de toepassing zijn hersteld, is deze hetzelfde als het aantal uitvoerender per LLAP-daemon. Voor D14 v2 is de aanbevolen waarde _* 19**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. de cache grootte van de LLAP-daemon bepalen**  
Configuratie: **_Hive. llap. io. Memory. grootte_* _

Het LLAP-daemon-container geheugen bestaat uit de volgende onderdelen: Hoofd kamer
*  Heap-geheugen dat wordt gebruikt door de (xmx)
*  In-memory cache per daemon (de geheugen grootte van de buiten-heap, niet van toepassing wanneer de SSD-cache is ingeschakeld)
*  Grootte van meta gegevens in cache geheugen (alleen van toepassing wanneer SSD-cache is ingeschakeld)

Beschik bare **grootte**: deze grootte geeft een deel van een off-heap geheugen aan dat wordt gebruikt voor de overhead van Java-vm's (meta-ruimte, threads stack, GC-gegevens structuren enzovoort). Over het algemeen is deze overhead ongeveer 6% van de Heap-grootte (xmx). Om aan de veiligste zijde te zijn, kan deze waarde worden berekend als 6% van de totale grootte van het LLAP-daemon-geheugen.  
Voor D14 v2 is de aanbevolen waarde ceil (80 GB x 0,06) ~ = **4 GB**.  

**Heap-grootte (xmx)**: dit is de hoeveelheid geheugen die beschikbaar is voor alle uitvoerders.
Totale grootte van heap = aantal uitvoerender x 3 GB  
Voor D14 v2 is deze waarde 19 x 3 GB = **57 GB**  

`Ambari environment variable for LLAP heap size:`

![Grootte van LLAP-heap](./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png "Grootte van LLAP-heap")

Wanneer SSD-cache is uitgeschakeld, is de in-memory cache de hoeveelheid geheugen die wordt overgelaten na het uitvallen van de grootte van de ruimte en de Heap-grootte van de LLAP-daemon-container grootte.

De berekening van de cache grootte wijkt af wanneer SSD-cache is ingeschakeld.
Als *Hive. llap. io. allocator. mmap* = True wordt ingesteld, wordt SSD-caching ingeschakeld.
Wanneer SSD-cache is ingeschakeld, wordt een gedeelte van het geheugen gebruikt voor het opslaan van meta gegevens voor de SSD-cache. De meta gegevens worden opgeslagen in het geheugen en er wordt naar verwachting ~ 8% van de SSD-cache grootte.   
Grootte van de meta gegevens in het geheugen van de SSD-cache = LLAP-daemon-container grootte-(head room + Heap-grootte)  
Voor D14 v2, met HDI 4,0, de grootte van de meta gegevens in het geheugen van SSD cache = 80 GB-(4 GB + 57 GB) = **19 GB**  
Voor D14 v2, met HDI 3,6, de grootte van de meta gegevens in het geheugen van SSD cache = 79 GB-(4 GB + 57 GB) = **18 GB**

Gezien de grootte van het beschik bare geheugen voor het opslaan van de meta gegevens van de SSD-cache, kunnen we de grootte van de SSD-cache berekenen die kan worden ondersteund.  
Grootte van de meta gegevens in het geheugen voor SSD-cache = LLAP-daemon-container grootte-(head room + Heap-grootte) = 19 GB     
Grootte van SSD-cache = grootte van meta gegevens in het geheugen voor SSD-cache (19 GB)/0,08 (8 procent)  

Voor D14 v2 en HDI 4,0, de aanbevolen cache grootte van SSD = 19 GB/0,08 ~ = **237 GB**  
Voor D14 v2 en HDI 3,6, de aanbevolen cache grootte van SSD = 18 GB/0,08 ~ = **225 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. het geheugen voor het koppelen van kaarten aanpassen**   
Configuratie: **_Hive. auto. Convert. samen voegen. noconditionaltask. grootte_* _

Zorg ervoor dat _hive. auto. Convert. noconditionaltask * is ingeschakeld voor deze para meter van kracht worden.
Deze configuratie bepaalt de drempel waarde voor MapJoin electie per Hive-Optimizer waarbij een overschakeling van het geheugen van andere uitvoerende modules wordt beschouwd om meer ruimte te bieden voor de hash-tabellen in het geheugen om meer toewijzings samenvoegings conversies toe te staan. Als er 3 GB per uitvoerder is, kan deze grootte worden geabonneerd op 3 GB, maar kan er ook een heap-geheugen worden gebruikt voor sorteer buffers, wille keurige buffers, enzovoort. door de andere bewerkingen.   
Voor D14 v2, met 3 GB geheugen per uitvoerder, wordt u aangeraden deze waarde in te stellen op **2048 MB**.  

(Opmerking: deze waarde moet mogelijk aanpassingen zijn die geschikt zijn voor uw werk belasting. Als u deze waarde te laag instelt, wordt de functie autoconvert niet gebruikt. En als u het te hoog instelt, kan dit leiden tot onvoldoende geheugen-uitzonde ringen of GC-onderbrekingen die kunnen leiden tot nadelige prestaties.)  

#### <a name="11-number-of-llap-daemons"></a>**11. aantal LLAP-daemons**
Ambari-omgevings variabelen: **_num_llap_nodes, num_llap_nodes_for_llap_daemons_* _  

_ *num_llap_nodes**: Hiermee geeft u het aantal knoop punten op dat door de Hive llap-service wordt gebruikt. Dit zijn onder andere knoop punten met llap daemon, Llap service Master en TEZ Application Master (TEZ am).  

![' Aantal knoop punten voor de LLAP-service '](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png "Aantal knoop punten voor de LLAP-service")  

**num_llap_nodes_for_llap_daemons** -opgegeven aantal knoop punten dat alleen wordt gebruikt voor llap-daemons. De grootte van LLAP-daemon-containers is ingesteld op het knoop punt Max fit, dus resulteert dit in één LLAP-daemon op elk knoop punt.

![' Aantal knoop punten voor LLAP-daemons '](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png "Aantal knoop punten voor LLAP-daemons")

Het is raadzaam beide waarden hetzelfde te laten als het aantal worker-knoop punten in het interactieve query cluster.

### <a name="considerations-for-workload-management"></a>**Overwegingen voor workload Management**  
Als u workload Management wilt inschakelen voor LLAP, moet u ervoor zorgen dat u voldoende capaciteit reserveert voor werkbelasting beheer om te functioneren zoals verwacht. Het workload Management vereist configuratie van een aangepaste garen wachtrij, die naast `llap` wachtrij is. Zorg ervoor dat u het totale aantal cluster resource capaciteit tussen llap wachtrij-en werkbelasting beheer wachtrij in overeenstemming brengt met de vereisten van uw werk belasting.
Met Workload management worden TEZ Application Masters (TEZ AMs) gestart wanneer een resource planning wordt geactiveerd.
Opmerking:

* TEZ AMs die worden geïnitieerd door een resource plan te activeren, verbruikt resources uit de wachtrij voor workload management zoals opgegeven door `hive.server2.tez.interactive.queue` .  
* Het aantal TEZ-AMs is afhankelijk van de waarde `QUERY_PARALLELISM` die is opgegeven in het resource-abonnement.  
* Zodra het werkbelasting beheer actief is, wordt TEZ AMs in llap-wachtrij niet gebruikt. Alleen TEZ AMs van de wachtrij voor werk belasting worden gebruikt voor de coördinatie van query's. TEZ AMs in de `llap` wachtrij worden gebruikt wanneer werkbelasting beheer is uitgeschakeld.
 
Bijvoorbeeld: totale cluster capaciteit = 100 GB geheugen, gedeeld tussen LLAP, Workload management en standaard wachtrijen:
 - llap-wachtrij capaciteit = 70 GB
 - Capaciteit van werkbelasting beheer wachtrij = 20 GB
 - Standaard capaciteit van de wachtrij = 10 GB

Met 20 GB in wachtrij capaciteit voor werkbelasting beheer kan een resource-abonnement een `QUERY_PARALLELISM` waarde van vijf opgeven, wat betekent dat werkbelasting beheer vijf TEZ AMS kan starten met elke container grootte van 4 GB. Als `QUERY_PARALLELISM` deze hoger is dan de capaciteit, ziet u mogelijk dat bepaalde TEZ AMS niet meer reageert in de `ACCEPTED` status. De Hiveserver2 Interactive kan geen query fragmenten verzenden naar de TEZ-AMs die niet in `RUNNING` staat zijn.


#### <a name="next-steps"></a>**Volgende stappen**
Als het probleem niet is opgelost met deze waarden, gaat u naar een van de volgende...

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](../../azure-portal/supportability/how-to-create-azure-support-request.md). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).  

* ##### <a name="other-references"></a>**Andere verwijzingen:**
  * [Andere eigenschappen van LLAP configureren](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [De Heap-grootte van de Hive-server configureren](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Geheugen grootte van kaart koppelen voor LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Eigenschappen van TEZ-uitvoerings engine](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [LLAP grondige kennis van Hive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)