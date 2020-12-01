---
title: Externe meta gegevens archieven gebruiken-Azure HDInsight
description: Gebruik externe meta gegevens archieven met Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/06/2020
ms.openlocfilehash: a001f3a13daf40a1af712f09e35d93fd045ea133
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350242"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Externe metagegevensopslag gebruiken in Azure HDInsight

Met HDInsight kunt u de controle over uw gegevens en meta gegevens overnemen met externe gegevens archieven. Deze functie is beschikbaar voor de [Apache Hive meta Store](#custom-metastore), [Apache Oozie meta Store](#apache-oozie-metastore)en [Apache Ambari data base](#custom-ambari-db).

De Apache Hive-meta Store in HDInsight is een essentieel onderdeel van de Apache Hadoop architectuur. Een meta Store is de centrale schema opslagplaats. De meta Store wordt gebruikt door andere big data Access-hulpprogram ma's, zoals Apache Spark, interactieve query (LLAP), Presto of Apache varken. HDInsight gebruikt een Azure SQL Database als de Hive-metastore.

![Architectuur van het meta gegevens archief van HDInsight Hive](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Er zijn twee manieren waarop u een meta Store voor uw HDInsight-clusters kunt instellen:

* [Standaard-META Store](#default-metastore)
* [Aangepaste meta Store](#custom-metastore)

## <a name="default-metastore"></a>Standaard-META Store

HDInsight maakt standaard een meta Store met elk cluster type. U kunt in plaats daarvan een aangepaste meta Store opgeven. De standaard-META Store bevat de volgende overwegingen:

* Geen extra kosten. HDInsight maakt een meta Store met elk cluster type zonder extra kosten voor u.

* Elke standaard-META Store maakt deel uit van de cluster levenscyclus. Wanneer u een cluster verwijdert, worden ook de corresponderende meta Store-gegevens verwijderd.

* U kunt de standaard META Store niet delen met andere clusters.

* Standaard-META Store wordt alleen aanbevolen voor eenvoudige werk belastingen. Werk belastingen waarvoor geen meerdere clusters zijn vereist en die geen meta gegevens nodig hebben die langer zijn dan de levens cyclus van het cluster.

> [!IMPORTANT]
> De standaard-META Store biedt een Azure SQL Database met een **elementaire 5 DTU-limiet (niet uitbreidbaar)**. Geschikt voor basis test doeleinden. Voor grote of productie werkbelasting raden wij u aan de migratie naar een externe meta Store te migreren.

## <a name="custom-metastore"></a>Aangepaste meta Store

HDInsight biedt ook ondersteuning voor aangepaste meta Stores, die worden aanbevolen voor productie clusters:

* U geeft uw eigen Azure SQL Database op als de meta Store.

* De levens cyclus van de meta Store is niet gebonden aan een cluster levenscyclus, zodat u clusters kunt maken en verwijderen zonder dat meta gegevens verloren gaan. Meta gegevens zoals uw Hive-schema's blijven behouden, zelfs nadat u het HDInsight-cluster hebt verwijderd en opnieuw hebt gemaakt.

* Met een aangepaste meta Store kunt u meerdere clusters en cluster typen aan die meta Store koppelen. Een voor beeld: een enkele meta Store kan worden gedeeld tussen interactieve query-, Hive-en Spark-clusters in HDInsight.

* U betaalt voor de kosten van een meta Store (Azure SQL Database) op basis van het prestatie niveau dat u kiest.

* U kunt de meta Store naar behoefte omhoog schalen.

* Het cluster en de externe meta Store moeten worden gehost in dezelfde regio.

![Use-case van het meta gegevens archief van HDInsight Hive](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Azure SQL Database maken en configureren voor de aangepaste meta Store

Maak een bestaande Azure SQL Database voordat u een aangepaste Hive-metastore voor een HDInsight-cluster instelt.  Zie [Quick Start: een enkele data base maken in Azure SQL database](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)voor meer informatie.

Tijdens het maken van het cluster moet de HDInsight-service verbinding maken met de externe meta Store en uw referenties verifiëren. Configureer Azure SQL Database firewall regels om Azure-Services en-bronnen toegang te geven tot de server. Schakel deze optie in het Azure Portal in door **Server firewall instellen** te selecteren. Selecteer vervolgens **niet** onder **open bare netwerk toegang weigeren** en **Ja** hieronder **toestaan dat Azure-Services en-bronnen toegang hebben tot deze server** voor Azure SQL database. Zie [IP-firewall regels maken en beheren](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) voor meer informatie.

Privé-eind punten voor SQL-archieven worden alleen ondersteund op de clusters die zijn gemaakt met `outbound` ResourceProviderConnection. Raadpleeg deze [documentatie](./hdinsight-private-link.md)voor meer informatie.

![knop Server firewall instellen](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![toegang tot Azure-Services toestaan](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Een aangepaste meta Store selecteren tijdens het maken van het cluster

U kunt uw cluster op elk gewenst moment naar een eerder gemaakt Azure SQL Database verwijzen. Voor het maken van een cluster via de portal, wordt de optie opgegeven uit de **opslag > de meta Store-instellingen**.

![Meta gegevens archief van HDInsight-Hive Azure Portal](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Hive-metastore richtlijnen

> [!NOTE]
> Gebruik waar mogelijk een aangepaste meta Store om reken resources (uw actieve cluster) en meta gegevens te scheiden (opgeslagen in het meta Store). Begin met de S2-laag, die 50 DTU en 250 GB opslag biedt. Als er een knel punt wordt weer geven, kunt u de data base omhoog schalen.

* Als u van plan bent meerdere HDInsight-clusters te gebruiken voor toegang tot afzonderlijke gegevens, gebruikt u een afzonderlijke Data Base voor de meta Store op elk cluster. Als u een meta Store deelt op meerdere HDInsight-clusters, betekent dit dat de clusters dezelfde data-en onderliggende gebruikers gegevens bestanden gebruiken.

* Maak regel matig een back-up van uw aangepaste meta Store. Azure SQL Database maakt automatisch back-ups, maar de periode voor het bewaren van back-ups varieert. Zie [informatie over automatische SQL database back-ups](../azure-sql/database/automated-backups-overview.md)voor meer informatie.

* Zoek de meta Store en het HDInsight-cluster in dezelfde regio. Deze configuratie biedt de hoogste prestaties en de laagste kosten voor het uitgaand verkeer van het netwerk.

* Bewaak uw meta Store voor prestaties en beschik baarheid met behulp van Azure SQL Database controle hulpprogramma's of Azure Monitor-Logboeken.

* Wanneer een nieuwe, hogere versie van Azure HDInsight wordt gemaakt op basis van een bestaande aangepaste meta store-data base, wordt het schema van de meta Store door het systeem bijgewerkt. De upgrade is onomkeerbaar zonder dat de data base vanuit een back-up wordt teruggezet.

* Als u een meta Store in meerdere clusters deelt, moet u ervoor zorgen dat alle clusters dezelfde HDInsight-versie zijn. Verschillende Hive-versies gebruiken verschillende meta Store-Database schema's. U kunt bijvoorbeeld geen meta Store delen via hive 2,1 en Hive 3,1 versie-clusters.

* In HDInsight 4,0 gebruiken Spark en Hive onafhankelijke catalogi om toegang te krijgen tot SparkSQL of Hive-tabellen. Een tabel gemaakt door Spark in de Spark-catalogus. Een tabel die door Hive is gemaakt, bevindt zich in de Hive-catalogus. Dit gedrag wijkt af van HDInsight 3,6 waarbij een gemeen schappelijke catalogus van Hive en Spark wordt gedeeld. De Hive-en Spark-integratie in HDInsight 4,0 is afhankelijk van Hive Warehouse connector (HWC). HWC werkt als een brug tussen Spark en Hive. [Meer informatie over Hive Warehouse connector](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

* Als u in HDInsight 4,0 de meta Store tussen Hive en Spark wilt delen, kunt u dit doen door de Property Store. catalog. default in te wijzigen in Hive in uw Spark-cluster. U kunt deze eigenschap vinden in Ambari Advanced spark2-component-site-override. Het is belang rijk om te begrijpen dat het delen van de meta Store alleen werkt voor externe Hive-tabellen. dit werkt niet als u interne/beheerde Hive-tabellen of zuur tabellen hebt.  

## <a name="apache-oozie-metastore"></a>Apache Oozie-meta Store

Apache Oozie is een coördinatiesysteem voor werkstromen waarmee Hadoop-taken worden beheerd. Oozie ondersteunt Hadoop-taken voor Apache MapReduce, Pig, Hive en anderen.  Oozie maakt gebruik van een meta Store voor het opslaan van gegevens over werk stromen. Als u de prestaties wilt verbeteren wanneer u Oozie gebruikt, kunt u Azure SQL Database als een aangepaste meta Store gebruiken. De meta Store biedt toegang tot Oozie-taak gegevens nadat u uw cluster hebt verwijderd.

Zie [Apache Oozie gebruiken voor werk stromen](hdinsight-use-oozie-linux-mac.md)voor instructies over het maken van een Oozie-meta store met Azure SQL database.

## <a name="custom-ambari-db"></a>Aangepaste Ambari-database

Als u uw eigen externe data base wilt gebruiken met Apache Ambari in HDInsight, raadpleegt u [Custom Apache Ambari data base](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Volgende stappen

* [Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer](./hdinsight-hadoop-provision-linux-clusters.md)