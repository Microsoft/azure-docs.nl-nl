---
title: Onderdelen voor hoge Beschik baarheid in azure HDInsight
description: Overzicht van de verschillende onderdelen voor hoge Beschik baarheid die worden gebruikt door HDInsight-clusters.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 93d2317c85f93ce8a22f2d434fbc081a88265a74
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863714"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Services met hoge Beschik baarheid die worden ondersteund door Azure HDInsight

HDInsight is ontwikkeld met een unieke architectuur voor hoge Beschik baarheid (HA) van essentiële services, zodat u een optimale Beschik baarheid kunt bieden voor uw analyse onderdelen. Sommige onderdelen van deze architectuur zijn ontwikkeld door micro soft om automatische failover te bieden. Andere onderdelen zijn standaard Apache-onderdelen die zijn geïmplementeerd ter ondersteuning van specifieke services. In dit artikel wordt de architectuur van het service model HA in HDInsight beschreven, hoe HDInsight failover ondersteunt voor HA-Services en best practices voor het herstellen van andere service onderbrekingen.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *Slave*, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

## <a name="high-availability-infrastructure"></a>Infra structuur met hoge Beschik baarheid

HDInsight biedt aangepaste infra structuur om ervoor te zorgen dat vier primaire services hoge Beschik baarheid met automatische failover-mogelijkheden hebben:

- Apache Ambari-server
- Tijdlijn server van de toepassing voor Apache-GARENs
- Taak geschiedenis server voor Hadoop-MapReduce
- Apache Livy

Deze infra structuur bestaat uit een aantal services en software onderdelen, waarvan sommige zijn ontworpen door micro soft. De volgende onderdelen zijn uniek voor het HDInsight-platform:

- Failover-controller voor slave
- Master failover-controller
- Service voor hoge Beschik baarheid slave
- Hoofd service voor hoge Beschik baarheid

:::image type="content" source="./media/hdinsight-high-availability-components/high-availability-architecture.png" alt-text="infra structuur met hoge Beschik baarheid" border="false":::

Er zijn ook andere services met hoge Beschik baarheid, die worden ondersteund door open-source Apache-betrouw bare onderdelen. Deze onderdelen zijn ook aanwezig in HDInsight-clusters:

- Hadoop File System (HDFS) NameNode
- GARENs-Resource Manager
- HBase Master

In de volgende secties vindt u meer informatie over de manier waarop deze services samen werken.

## <a name="hdinsight-high-availability-services"></a>Services met hoge Beschik baarheid HDInsight

Micro soft biedt ondersteuning voor de vier Apache-Services in de volgende tabel in HDInsight-clusters. Om ze te onderscheiden van services met hoge Beschik baarheid die worden ondersteund door onderdelen van Apache, worden de *Services van HDINSIGHT ha* genoemd.

| Service | Clusterknooppunten | Cluster typen | Doel |
|---|---|---|---|
| Apache Ambari-server| Actieve hoofd knooppunt | Alles | Bewaakt en beheert het cluster.|
| Tijdlijn server van de toepassing voor Apache-GARENs | Actieve hoofd knooppunt | Alle behalve Kafka | Onderhoudt fout opsporingsgegevens over garen taken die op het cluster worden uitgevoerd.|
| Taak geschiedenis server voor Hadoop-MapReduce | Actieve hoofd knooppunt | Alle behalve Kafka | Onderhoudt gegevens van fout opsporing voor MapReduce-taken.|
| Apache Livy | Actieve hoofd knooppunt | Spark | Maakt eenvoudige interactie met een Spark-cluster mogelijk via een REST-interface |

>[!Note]
> HDInsight-clusters (ESP-Enterprise Security Package) bieden momenteel alleen de hoge Beschik baarheid van de Ambari-server. De server van de toepassings tijdlijn, de taak geschiedenis server en livy worden allemaal alleen op headnode0 uitgevoerd en er worden geen failover naar headnode1 wanneer Ambari failsover. De data base van de toepassings tijdlijn bevindt zich ook in headnode0 en niet op Ambari SQL Server.

### <a name="architecture"></a>Architectuur

Elk HDInsight-cluster heeft respectievelijk twee hoofd knooppunten in de modi actief en stand-by. De HDInsight HA-services worden alleen op hoofd knooppunten uitgevoerd. Deze services moeten altijd worden uitgevoerd op het actieve hoofd knooppunt en in de onderhouds modus worden gestopt en in de stand-bymodus worden geplaatst hoofd knooppunt.

Om de juiste statussen van HA-services te behouden en een snelle failover te bieden, maakt HDInsight gebruik van Apache ZooKeeper, een coördinatie service voor gedistribueerde toepassingen, om actieve hoofd knooppunt-verkiezing uit te voeren. HDInsight is ook van toepassing op een aantal Java-achtergrond processen, waarmee de procedure failover voor HDInsight HA-Services wordt gecoördineerd. Deze services zijn de volgende: de hoofd-failover-controller, de slave-failover-controller, de *Master-ha-service* en de *Slave-ha-service*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper is een krachtige coördinatie service voor gedistribueerde toepassingen. In productie wordt ZooKeeper doorgaans uitgevoerd in de gerepliceerde modus waarbij een gerepliceerde groep van ZooKeeper-servers een quorum vormt. Elk HDInsight-cluster heeft drie ZooKeeper-knoop punten waarmee drie ZooKeeper-servers een quorum kunnen vormen. HDInsight heeft twee ZooKeeper-quorums die parallel met elkaar worden uitgevoerd. Met één quorum wordt de actieve hoofd knooppunt bepaald in een cluster waarop de services van HDInsight HA moeten worden uitgevoerd. Een ander quorum wordt gebruikt voor het coördineren van HA-services van Apache, zoals beschreven in latere secties.

### <a name="slave-failover-controller"></a>Failover-controller voor slave

De slave-failover-controller wordt uitgevoerd op elk knoop punt in een HDInsight-cluster. Deze controller is verantwoordelijk voor het starten van de Ambari-agent en de *Slave-ha-service* op elk knoop punt. Periodiek wordt een query uitgevoerd op het eerste ZooKeeper-quorum over de actieve hoofd knooppunt. Wanneer de actieve en stand-hoofd knooppunten worden gewijzigd, voert de slave-failover-controller de volgende handelingen uit:

1. Hiermee wordt het configuratie bestand van de host bijgewerkt.
1. Hiermee wordt de Ambari-agent opnieuw gestart.

De *Slave-ha-service* is verantwoordelijk voor het stoppen van de services van HDInsight ha (met uitzonde ring van Ambari-server) in de stand-bymodus hoofd knooppunt.

### <a name="master-failover-controller"></a>Master failover-controller

Een hoofd-failover-controller wordt uitgevoerd op beide hoofd knooppunten. Beide Master failover-controllers communiceren met het eerste ZooKeeper-quorum om de hoofd knooppunt te benoemen waarop ze worden uitgevoerd als de actieve hoofd knooppunt.

Als de hoofd failover-controller bijvoorbeeld op hoofd knooppunt 0 de verkiezing wint, worden de volgende wijzigingen aangebracht:

1. Hoofd knooppunt 0 wordt actief.
1. De Master-failover-controller start de Ambari-server en de *Master-ha-service* op hoofd knooppunt 0.
1. De andere Master failover controller stopt de Ambari-server en de *Master-ha-service* op hoofd knooppunt 1.

De Master-ha-service wordt alleen uitgevoerd op de actieve hoofd knooppunt, de HDInsight HA-Services (met uitzonde ring van Ambari-server) op stand-by hoofd knooppunt worden gestopt en worden gestart op actieve hoofd knooppunt.

### <a name="the-failover-process"></a>Het failover-proces

:::image type="content" source="./media/hdinsight-high-availability-components/failover-steps.png" alt-text="failoverproces" border="false":::

Er wordt een health monitor uitgevoerd op elke hoofd knooppunt samen met de Master-failover-controller om heartbeat-meldingen te verzenden naar het Zookeeper-quorum. De hoofd knooppunt wordt in dit scenario beschouwd als HA-service. De health monitor controleert of elke service voor hoge Beschik baarheid in orde is en of deze klaar is om aan de leiderschaps verkiezing toe te voegen. Zo ja, dan zal deze hoofd knooppunt concurreren met de verkiezing. Als dat niet het geval is, wordt de verkiezing afgesloten totdat deze weer gereed wordt.

Als de stand-hoofd knooppunt ooit het leiderschap realiseert en actief wordt (zoals in het geval van een storing met het vorige actieve knoop punt), start de Master failover controller alle services van HDInsight HA erop. De Master failover controller stopt deze services ook op de andere hoofd knooppunt.

Voor service fouten van HDInsight HA, zoals een service die niet actief is of niet in orde is, moet de hoofd failover-controller de services automatisch opnieuw opstarten of stoppen op basis van de status van de hoofd knooppunt. Gebruikers hoeven geen services van HDInsight HA hand matig te starten op beide hoofd knooppunten. In plaats daarvan staat automatische of hand matige failover toe om de service te herstellen.

### <a name="inadvertent-manual-intervention"></a>Per ongeluk hand matige interventie

HDInsight HA-services mogen alleen worden uitgevoerd op de actieve hoofd knooppunt en worden automatisch opnieuw gestart wanneer dit nodig is. Aangezien afzonderlijke HA-Services geen eigen gezondheids monitor hebben, kan failover niet worden geactiveerd op het niveau van de afzonderlijke service. Failover wordt gegarandeerd op knooppunt niveau en niet op service niveau.

### <a name="some-known-issues"></a>Enkele bekende problemen

- Wanneer een service van HA hand matig wordt gestart op de stand-by-hoofd knooppunt, stopt deze pas wanneer de volgende failover plaatsvindt. Wanneer HA-services worden uitgevoerd op beide hoofd knooppunten, zijn er enkele mogelijke problemen: de Ambari-gebruikers interface is niet toegankelijk, Ambari genereert fouten, GARENs, vonken en Oozie taken kunnen vastlopen.

- Wanneer een HA-service op de actieve hoofd knooppunt stopt, wordt deze niet opnieuw opgestart totdat de volgende failover wordt uitgevoerd of de hoofd failover-controller/master-ha-service opnieuw wordt gestart. Wanneer een of meer HA-services stoppen met de actieve hoofd knooppunt, met name wanneer Ambari-server stopt, Ambari gebruikers interface niet toegankelijk is, kunnen andere potentiële problemen bestaan uit GARENs van het ene of het Oozie-project.

## <a name="apache-high-availability-services"></a>Apache-Services met hoge Beschik baarheid

Apache biedt hoge Beschik baarheid voor HDFS NameNode, garen Resource Manager en HBase Master, die ook beschikbaar zijn in HDInsight-clusters. In tegens telling tot HDInsight HA-services worden ze ondersteund in ESP-clusters. Apache HA-services communiceren met het tweede ZooKeeper-quorum (beschreven in de bovenstaande sectie) om statussen actief/stand-by te kiezen en automatische failover uit te voeren. De volgende secties bevatten informatie over de werking van deze services.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop Distributed File System (HDFS) NameNode

HDInsight-clusters op basis van Apache Hadoop 2,0 of hoger bieden NameNode hoge Beschik baarheid. Er worden twee NameNodes uitgevoerd op de hoofd knooppunten, die zijn geconfigureerd voor automatische failover. De NameNodes gebruiken de *ZKFailoverController* om te communiceren met Zookeeper om te kiezen voor de status actief/stand-by. De *ZKFailoverController* wordt uitgevoerd op beide hoofd knooppunten en werkt op dezelfde manier als de bovenstaande Master-failover-controller.

Het tweede Zookeeper-quorum is onafhankelijk van het eerste quorum, zodat de actieve NameNode mogelijk niet wordt uitgevoerd op de actieve hoofd knooppunt. Wanneer de actieve NameNode dood of slecht is, wordt de stand-NameNode van de verkiezing gewonnen en actief.

### <a name="yarn-resourcemanager"></a>GARENs-Resource Manager

HDInsight-clusters op basis van Apache Hadoop 2,4 of hoger ondersteunen ondersteuning voor GARENs met hoge Beschik baarheid. Er zijn twee ResourceManagers, RM1 en RM2, die respectievelijk worden uitgevoerd op hoofd knooppunt 0 en hoofd knooppunt 1. Net als NameNode is garen-Resource Manager ook geconfigureerd voor automatische failover. Er wordt automatisch een andere resource manager gekozen om actief te zijn wanneer de huidige actieve Resource Manager uitvalt of niet meer reageert.

GARENs van de Resource Manager maakt gebruik van de Inge sloten *ActiveStandbyElector* als fout detector en Leader. In tegens telling tot HDFS NameNode heeft garen-Resource Manager geen afzonderlijke ZKFC-daemon nodig. De actieve Resource Manager schrijft de statussen naar Apache Zookeeper.

De hoge Beschik baarheid van de garen-Resource Manager is onafhankelijk van NameNode en andere HDInsight HA-Services. De actieve Resource Manager kan niet worden uitgevoerd op de actieve hoofd knooppunt of de hoofd knooppunt waarop de actieve NameNode wordt uitgevoerd. Zie [hoge Beschik baarheid in de Resource Manager](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)voor meer informatie over de hoge beschik BAARHEID van garen-Resource Manager.

### <a name="hbase-master"></a>HBase Master

HDInsight HBase-clusters ondersteunen HBase Master hoge Beschik baarheid. In tegens telling tot andere HA-Services, die worden uitgevoerd op hoofd knooppunten, worden HBase-Masters uitgevoerd op de drie Zookeeper-knoop punten, waarbij een van beide de actieve hoofd server is en de andere twee stand-by. Net als NameNode, HBase Master coördinaten met Apache Zookeeper voor Leader verkiezing en voert automatische failover uit wanneer de huidige actieve Master problemen heeft. Er is slechts één actieve HBase Master op elk gewenst moment.

## <a name="next-steps"></a>Volgende stappen

- [Beschik baarheid en betrouw baarheid van Apache Hadoop clusters in HDInsight](./hdinsight-business-continuity.md)
- [Azure HDInsight Virtual Network-architectuur](hdinsight-virtual-network-architecture.md)