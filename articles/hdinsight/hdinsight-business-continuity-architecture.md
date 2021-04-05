---
title: Azure HDInsight-architectuur voor bedrijfs continuïteit
description: In dit artikel worden de verschillende mogelijke bedrijfs continuïteits architecturen voor HDInsight beschreven
keywords: Hadoop hoge Beschik baarheid
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f74309370f1489714013344116e7feb9551fbfd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933454"
---
# <a name="azure-hdinsight-business-continuity-architectures"></a>Azure HDInsight-architectuur voor bedrijfs continuïteit

Dit artikel bevat enkele voor beelden van bedrijfs continuïteits architecturen die u kunt overwegen voor Azure HDInsight. Tolerantie voor verminderde functionaliteit tijdens een nood geval is een bedrijfs beslissing die van de ene toepassing in de volgende kan verschillen. Het kan acceptabel zijn dat sommige toepassingen niet beschikbaar zijn of deels beschikbaar zijn met beperkte functionaliteit of vertraagde verwerking gedurende een periode. Voor andere toepassingen kan de gereduceerde functionaliteit onaanvaardbaar zijn.

> [!NOTE]
> De architecturen die in dit artikel worden gepresenteerd, zijn op geen enkele manier limitatief. U moet uw eigen unieke architecturen ontwerpen wanneer u objectieve bepalingen hebt opgesteld rond de verwachte bedrijfs continuïteit, operationele complexiteit en eigendoms kosten.

## <a name="apache-hive-and-interactive-query"></a>Apache Hive en interactieve query

[Hive-replicatie v2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development#HiveReplicationv2Development-REPLSTATUS) wordt aanbevolen voor bedrijfs continuïteit in HDInsight-Hive-en interactieve query clusters. De permanente secties van een zelfstandige Hive-cluster die moeten worden gerepliceerd, zijn de opslaglaag en de Hive-metastore. Hive-clusters in een scenario met meerdere gebruikers met Enterprise Security Package Azure Active Directory Domain Services en zwerver meta Store nodig hebben.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hive-interactive-query.png" alt-text="Architectuur van Hive-en interactieve query's":::

Replicatie op basis van Hive-gebeurtenissen is geconfigureerd tussen de primaire en secundaire clusters. Dit bestaat uit twee afzonderlijke fasen, Boots trap en incrementele uitvoeringen:

* Met Boots trap ping wordt het hele Hive-Warehouse gerepliceerd, met inbegrip van de Hive-metastore informatie van primair naar secundair.

* Incrementele uitvoeringen worden geautomatiseerd op het primaire cluster en de gebeurtenissen die tijdens de incrementele uitvoeringen worden gegenereerd, worden weer gegeven op het secundaire cluster. Het secundaire cluster wordt opgeteld met de gebeurtenissen die zijn gegenereerd op basis van de primaire cluster, zodat het secundaire cluster consistent is met de gebeurtenissen van het primaire cluster nadat de replicatie is uitgevoerd.

Het secundaire cluster is alleen nodig op het moment van replicatie voor het uitvoeren van een gedistribueerde kopie, `DistCp` maar de opslag-en meta Stores moeten permanent zijn. U kunt ervoor kiezen om vóór de replicatie een secundair cluster op aanvraag in een script in te stellen, het replicatie script uit te voeren en het na een geslaagde replicatie te verbreken.

Het secundaire cluster is doorgaans alleen-lezen. U kunt het secundaire cluster lezen/schrijven, maar dit voegt extra complexiteit toe, waarbij de wijzigingen van het secundaire cluster naar het primaire cluster worden gerepliceerd.

### <a name="hive-event-based-replication-rpo--rto"></a>Op gebeurtenissen gebaseerde RPO-& van de Hive-RTO

* RPO: gegevens verlies is beperkt tot de laatste geslaagde incrementele replicatie gebeurtenis van de primaire naar de secundaire.

* RTO: de tijd tussen het mislukken en de hervatting van upstream-en downstream-trans acties met de secundaire.

### <a name="apache-hive-and-interactive-query-architectures"></a>Apache Hive-en interactieve query architecturen

#### <a name="hive-active-primary-with-on-demand-secondary"></a>Hive actief primair met on-demand secundair

In een *actieve primaire, met secundaire architectuur op aanvraag* , schrijven toepassingen naar de actieve primaire regio terwijl er geen cluster in de secundaire regio wordt ingericht tijdens normale bewerkingen. SQL-meta Store en opslag in de secundaire regio zijn permanent, terwijl het HDInsight-cluster wordt gescripteerd en alleen op aanvraag wordt geïmplementeerd voordat de geplande Hive-replicatie wordt uitgevoerd.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary.png" alt-text="actief primair met on-demand secundair":::

#### <a name="hive-active-primary-with-standby-secondary"></a>Hive actief primair met stand-by-secundair

In een *actief primair met stand-by secundair*, schrijven toepassingen naar de actieve primaire regio terwijl een stand-by omlaag geschaald secundair cluster in de modus alleen-lezen wordt uitgevoerd tijdens normale bewerkingen. Tijdens normale bewerkingen kunt u ervoor kiezen om regio-specifieke Lees bewerkingen te offloaden naar een secundaire.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary.png" alt-text="actief primair met stand-by-secundair":::

Zie [Apache Hive replicatie in azure HDInsight-clusters](./interactive-query/apache-hive-replication.md) voor meer informatie over Hive-replicatie en code voorbeelden.

## <a name="apache-spark"></a>Apache Spark

Spark-workloads kunnen al dan niet een Hive-component omvatten. Om Spark SQL-workloads in te scha kelen om gegevens van Hive te lezen en te schrijven, delen HDInsight Spark-clusters aangepaste meta Stores van Hive-en interactieve query-clusters in dezelfde regio. In dergelijke gevallen moet de replicatie van de Spark-workloads naar de andere regio ook worden meegestuurd met de replicatie van Hive-meta Stores en storage. De failover-scenario's in deze sectie zijn van toepassing op beide:

* [Spark SQL op zure tabellen die gebruikmaken van HWC-installatie (component Warehouse connector)](./interactive-query/apache-hive-warehouse-connector.md) met behulp van een HDInsight-cluster voor interactieve query's.
* Spark SQL-werk belasting op niet-zure tabellen met een HDInsight Hadoop-cluster.

Voor scenario's waarbij Spark in de zelfstandige modus werkt, moeten gegevens en opgeslagen Spark-potten (voor livy-taken) regel matig worden gerepliceerd van de primaire regio naar de secundaire regio, met behulp van Azure Data Factory `DistCP` .

U wordt aangeraden versie besturings systemen te gebruiken om Spark-notebooks en-bibliotheken op te slaan, waar ze eenvoudig kunnen worden geïmplementeerd op primaire of secundaire clusters. Zorg ervoor dat oplossingen op basis van notitie blokken en niet-notebooks zijn voor bereid op het laden van de juiste gegevens koppeling in de primaire of secundaire werk ruimte.

Als er klantspecifieke bibliotheken zijn die meer dan HDInsight bieden, moeten ze worden getraceerd en periodiek worden geladen in de stand-by secundaire cluster.  

### <a name="apache-spark-replication-rpo--rto"></a>Apache Spark replicatie-RPO & RTO

* RPO: het gegevens verlies is beperkt tot de laatste geslaagde incrementele replicatie (Spark en Hive) van de primaire naar de secundaire replica.

* RTO: de tijd tussen het mislukken en de hervatting van upstream-en downstream-trans acties met de secundaire.

### <a name="apache-spark-architectures"></a>Apache Spark architecturen

#### <a name="spark-active-primary-with-on-demand-secondary"></a>Spark actief primair met on-demand secundair

Toepassingen lezen en schrijven naar Spark-en Hive-clusters in de primaire regio terwijl er geen clusters in de secundaire regio worden ingericht tijdens de normale werking. SQL meta Store, Hive-opslag en Spark-opslag zijn permanent in de secundaire regio. De Spark-en Hive-clusters worden op aanvraag gescripteerd en geïmplementeerd. Hive-replicatie wordt gebruikt om Hive-opslag en Hive-meta Stores te repliceren, terwijl Azure Data Factory `DistCP` kan worden gebruikt voor het kopiëren van zelfstandige Spark-opslag. Hive-clusters moeten worden geïmplementeerd voordat elke Hive-replicatie wordt uitgevoerd vanwege de afhankelijkheids `DistCp` berekening.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary-spark.png" alt-text="actief primair met secundaire Apache Spark architectuur op aanvraag":::

#### <a name="spark-active-primary-with-standby-secondary"></a>Spark actief primair met stand-by-secundair

Toepassingen lezen en schrijven naar Spark-en Hive-clusters in de primaire regio terwijl stand-by omlaag geschaalde componenten en Spark-clusters in de modus alleen-lezen worden uitgevoerd in de secundaire regio tijdens normale bewerkingen. Tijdens de normale bewerkingen kunt u ervoor kiezen om regio-specifieke Hive en Spark-Lees bewerkingen te offloaden naar een secundaire.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary-spark.png" alt-text="actieve primaire stand-by secundaire Apache Spark ":::

## <a name="apache-hbase"></a>Apache HBase

HBase export en HBase-replicatie zijn algemene manieren om bedrijfs continuïteit in te scha kelen tussen HDInsight HBase-clusters.

HBase export is een batch replicatie proces dat gebruikmaakt van het export hulpprogramma HBase voor het exporteren van tabellen van het primaire HBase-cluster naar de onderliggende Azure Data Lake Storage gen 2-opslag. De geëxporteerde gegevens kunnen vervolgens worden geopend vanuit het secundaire HBase-cluster en worden geïmporteerd in tabellen die moeten bestaan in de secundaire. Hoewel de HBase-export een granulatie op tabel niveau biedt, bepaalt de Automation-engine voor het exporteren van het aantal incrementele rijen dat in elke uitvoering moet worden meegenomen. Zie [HDInsight HBase Backup and Replication](./hbase/apache-hbase-backup-replication.md#export-then-import)(Engelstalig) voor meer informatie.

HBase-replicatie maakt gebruik van bijna realtime replicatie tussen HBase-clusters op een volledig geautomatiseerde manier. De replicatie wordt uitgevoerd op tabel niveau. Alle tabellen of specifieke tabellen kunnen worden benaderd voor replicatie. HBase-replicatie is uiteindelijk consistent, wat inhoudt dat recente wijzigingen in een tabel in de primaire regio mogelijk niet onmiddellijk beschikbaar zijn voor alle secundairen. De secundaire zones worden gegarandeerd uiteindelijk consistent met de primaire. HBase-replicatie kan tussen twee of meer HDInsight HBase-clusters worden ingesteld als:

* Primair en secundair bevinden zich in hetzelfde virtuele netwerk.
* Primair en secundair bevinden zich in verschillende gepeerde VNets in dezelfde regio.
* Primair en secundair bevinden zich in verschillende gepeerde VNets in verschillende regio's.

Zie voor meer informatie [configuratie van Apache HBase-cluster replicatie in azure Virtual Networks](./hbase/apache-hbase-replication.md).

Er zijn een aantal andere manieren voor het uitvoeren van back-ups van HBase-clusters, zoals [het kopiëren van de map HBase, het](./hbase/apache-hbase-backup-replication.md#copy-the-hbase-folder) [kopiëren van tabellen](./hbase/apache-hbase-backup-replication.md#copy-tables) en [moment opnamen](./hbase/apache-hbase-backup-replication.md#snapshots).

### <a name="hbase-rpo--rto"></a>HBase RPO & RTO

#### <a name="hbase-export"></a>HBase exporteren

* RPO: gegevens verlies is beperkt tot de laatste voltooide batch import door de secundaire van de primaire.
* RTO: de tijd tussen het mislukken van de primaire en hervatting van I/O-bewerkingen op de secundaire.

#### <a name="hbase-replication"></a>HBase-replicatie

* RPO: gegevens verlies is beperkt tot de laatste WalEdit-verzen ding die is ontvangen op het secundaire.
* RTO: de tijd tussen het mislukken van de primaire en hervatting van I/O-bewerkingen op de secundaire.

### <a name="hbase-architectures"></a>HBase architecturen

HBase-replicatie kan in drie modi worden ingesteld: Leader-volger, Leader-Leader en cyclisch.

#### <a name="hbase-replication--leader--follower-model"></a>HBase-replicatie: label-model van follower

In deze regio die is ingesteld, is replicatie van de primaire regio naar de secundaire regio. Alle tabellen of specifieke tabellen op de primaire tabel kunnen worden geïdentificeerd voor replicatie in één richting. Tijdens normale bewerkingen kan het secundaire cluster worden gebruikt voor het leveren van Lees aanvragen in een eigen regio.

Het secundaire cluster fungeert als een normaal HBase-cluster dat een eigen tabel kan hosten en lees-en schrijf bewerkingen kan uitvoeren vanuit regionale toepassingen. Schrijf bewerkingen voor de gerepliceerde tabellen of tabellen die van de oorspronkelijke naar de secundaire tabel worden uitgevoerd, worden echter niet teruggerepliceerd naar de primaire.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-follower.png" alt-text="Model voor HBase Leader":::

#### <a name="hbase-replication--leader--leader-model"></a>HBase-replicatie: toonaangevend model

Deze regio die u hebt ingesteld, is vergelijkbaar met de installatie in één richting, behalve dat replicatie twee maal gebeurt tussen de primaire regio en de secundaire regio. Toepassingen kunnen beide clusters gebruiken in de modus lezen-schrijven en updates zijn asynchroon van elkaar.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-leader.png" alt-text="HBase Leader-model":::

#### <a name="hbase-replication-multi-region-or-cyclic"></a>HBase-replicatie: meerdere regio's of cyclisch

Het replicatie model Multi-Region/cyclische is een uitbrei ding van HBase-replicatie en kan worden gebruikt voor het maken van een wereld wijd redundante HBase-architectuur met meerdere toepassingen die lees-en schrijf bewerkingen naar specifieke HBase-clusters van regio's. De clusters kunnen worden ingesteld in verschillende combi Naties van leider/leider of leider/volger, afhankelijk van de bedrijfs vereisten.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-cyclic.png" alt-text="Cyclisch model HBase":::

## <a name="apache-kafka"></a>Apache Kafka

De beschik baarheid van meerdere regio's inschakelen HDInsight 4,0 ondersteunt Kafka MirrorMaker die kunnen worden gebruikt voor het onderhouden van een secundaire replica van het primaire Kafka-cluster in een andere regio. MirrorMaker fungeert als een consumer op hoog niveau, gebruikt van een specifiek onderwerp in het primaire cluster en maakt een onderwerp met dezelfde naam in de secundaire. Replicatie tussen clusters voor nood herstel met hoge Beschik baarheid met behulp van MirrorMaker wordt geleverd met de veronderstelling dat producenten en consumenten een failover naar het replica cluster moeten uitvoeren. Zie [MirrorMaker-onderwerpen Apache Kafka repliceren met Kafka op HDInsight](./kafka/apache-kafka-mirroring.md) voor meer informatie.

Afhankelijk van de levens duur van het onderwerp wanneer de replicatie is gestart, kan de MirrorMaker-onderwerp replicatie leiden tot verschillende offsets tussen de bron-en replica-onderwerpen. HDInsight Kafka-clusters bieden ook ondersteuning voor de replicatie van onderwerps partities. Dit is een functie voor hoge Beschik baarheid op het niveau van de afzonderlijke cluster.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-replication.png" alt-text="Replicatie Apache Kafka":::

### <a name="apache-kafka-architectures"></a>Apache Kafka architecturen

#### <a name="kafka-replication-active--passive"></a>Kafka-replicatie: actief: passief

Met Active-Passive Setup kunt u asynchrone eenrichtings spiegeling van actief naar passief maken. Producenten en consumenten moeten op de hoogte zijn van het bestaan van een actief en passief cluster en moeten voor het uitvoeren van een failover naar de passief kunnen worden uitgevoerd voor het geval het actief mislukt. Hieronder vindt u enkele voor delen en nadelen van Active-Passive Setup.

Voordelen:

* De netwerk latentie tussen clusters heeft geen invloed op de prestaties van het actieve cluster.
* Eenvoud van een replicatie in één richting.

Nadelen:

* Het passieve cluster kan nog niet worden gebruikt.
* Ontwerp complexiteit in het opnemen van failover-bewustmaking in toepassings producenten en consumenten.
* Mogelijk gegevens verlies tijdens uitval van het actieve cluster.
* Uiteindelijke consistentie tussen de onderwerpen tussen actieve en passieve clusters.
* Failback naar primair kan leiden tot inconsistentie van berichten in onderwerpen.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-passive.png" alt-text="Actief passieve model Apache Kafka":::

#### <a name="kafka-replication-active--active"></a>Kafka-replicatie: actief – actief

Active-Active instellen zijn twee regionale, VNet-peered HDInsight Kafka-clusters met bidirectionele asynchrone replicatie met MirrorMaker. In dit ontwerp worden berichten die door de consumenten in de primaire gebruikers worden gebruikt, ook beschikbaar gesteld aan consumenten in secundaire en vice versa. Hieronder vindt u enkele voor delen en nadelen van Active-Active Setup.

Voordelen:

* Vanwege hun gedupliceerde status kunnen failovers en failbacks eenvoudiger worden uitgevoerd.

Nadelen:

* Instellen, beheren en bewaken is ingewik kelder dan actief-passief.
* Het probleem van circulaire replicatie moet worden verholpen.  
* Bidirectionele replicatie leidt tot hogere kosten voor het uitvallen van regionale gegevens.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-active.png" alt-text="Actief actief model Apache Kafka":::

## <a name="hdinsight-enterprise-security-package"></a>HDInsight-Enterprise Security Package

Deze instelling wordt gebruikt voor het inschakelen van functionaliteit voor meerdere gebruikers in zowel primaire als secundaire en [Azure AD DS replica sets](../active-directory-domain-services/tutorial-create-replica-set.md) , om ervoor te zorgen dat gebruikers zich kunnen verifiëren voor beide clusters. Tijdens normaal gebruik moeten zwerver-beleids regels worden ingesteld in de secundaire, om ervoor te zorgen dat gebruikers alleen lees bewerkingen kunnen uitvoeren. In de onderstaande architectuur wordt uitgelegd hoe een door ESP ingeschakelde component actieve primaire – stand-by secundaire set kan worden weer gegeven.

Zwerver meta Store-replicatie:

De meta Store van Zwerver wordt gebruikt voor het permanent opslaan en verwerken van Zwerver-beleid voor het beheer van de gegevens autorisatie. We raden u aan het onafhankelijke zwerver-beleid in primaire en secundaire beleids regels te onderhouden en de secundaire als een lees replica te onderhouden.
  
Als het nodig is om zwerver-beleid te synchroniseren tussen de primaire en de secundaire, gebruikt u [zwerver importeren/exporteren](https://cwiki.apache.org/confluence/display/RANGER/User+Guide+For+Import-Export) om periodiek back-ups te maken en het beleid voor zwerver te importeren van primair naar secundair.

Het repliceren van Zwerver-beleid tussen primair en secundair kan ertoe leiden dat de secundaire functie voor schrijven is ingeschakeld, wat kan leiden tot onopzettelijke schrijf bewerkingen op de secundaire, waardoor de gegevens inconsistent zijn.  

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hdinsight-enterprise-security-package.png" alt-text="Enterprise Security Package architectuur van HDInsight":::

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de items die in dit artikel worden besproken:

* [Azure HDInsight-bedrijfs continuïteit](./hdinsight-business-continuity.md)
* [Casestudy Azure HDInsight met hoge Beschik baarheid voor oplossings architectuur](./hdinsight-high-availability-case-study.md)
* [Wat is Apache Hive en HiveQL in azure HDInsight?](./hadoop/hdinsight-use-hive.md)