---
title: Wat is Apache HBase in Azure HDInsight?
description: Een inleiding tot Apache HBase in HDInsight, een NoSQL-database gebaseerd op Hadoop. Meer informatie over de toepassingsmogelijkheden en HBase vergelijken met andere Hadoop-clusters.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 98872cc1315d946c63825318d2b98460031e128a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942903"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Wat is Apache HBase in Azure HDInsight

[Apache HBase](https://hbase.apache.org/) is een open-source NoSQL-database die is gebaseerd op Apache Hadoop en die is gemodelleerd naar [Google BigTable](https://cloud.google.com/bigtable/). HBase biedt willekeurige toegang en een sterke consistentie voor grote hoeveelheden gegevens in een database zonder schema. De database is georganiseerd op basis van kolomfamilies.

Vanuit het perspectief van de gebruikers is HBase vergelijkbaar met een database. De gegevens worden opgeslagen in de rijen en kolommen van een tabel en de gegevens in een rij worden gegroepeerd op basis van de kolomfamilie. HBase is een database zonder schema. De kolommen en gegevenstypen kunnen ongedefinieerd zijn voordat u ze gebruikt. De open-source code wordt lineair geschaald om petabytes aan gegevens op duizenden knooppunten te verwerken. Hiervoor kan gebruik worden gemaakt van gegevensredundantie, batchverwerking en andere functies die worden geboden door gedistribueerde toepassingen in de Hadoop-omgeving.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Hoe wordt Apache HBase geïmplementeerd in Azure HDInsight?

HDInsight HBase wordt aangeboden als een beheerd cluster dat is geïntegreerd in de Azure-omgeving. De clusters zijn geconfigureerd om gegevens rechtstreeks op te slaan in [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md), voor een lage latentie en verbeterde elasticiteit met betrekking tot de prestatie- en kostenopties. Met deze eigenschap kunnen klanten interactieve websites bouwen die werken met grote gegevenssets. Zo kunnen ze services bouwen die sensor- en telemetriegegevens van miljoenen eindpunten opslaan. En deze gegevens analyseren met Hadoop-taken. HBase en Hadoop zijn goede uitgangspunten voor een big data-project in Azure. Met de services kunnen realtime-toepassingen werken met grote gegevenssets.

De HDInsight-implementatie maakt gebruik van de uitschaalarchitectuur van HBase om automatische sharding van tabellen te bieden. En sterke consistentie voor lees- en schrijfbewerkingen en automatische failover. De prestaties zijn verbeterd dankzij in-memory caching voor leesbewerkingen en streamen met een hoge gegevensdoorvoer voor schrijfbewerkingen. Een HBase-cluster kan worden gemaakt in het virtuele netwerk. Zie [HDInsight-clusters maken in Azure Virtual Network](./apache-hbase-provision-vnet.md) voor meer informatie.

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hoe worden gegevens in HDInsight HBase beheerd?

Gegevens kunnen worden beheerd in HBase met de opdrachten `create`, `get`, `put` en `scan` in de HBase-shell. Gegevens worden met `put` naar de database geschreven en gelezen met `get`. De opdracht `scan` wordt gebruikt om gegevens uit meerdere rijen in een tabel op te halen. Gegevens kunnen ook worden beheerd met de HBase C# API, die naast de HBase REST API een clientbibliotheek biedt. Er kunnen ook query's op de HBase-database worden uitgevoerd met [Apache Hive](https://hive.apache.org/). Zie [Aan de slag met Apache HBase met Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md) voor een inleiding over deze programmeermodellen. Er zijn ook co-processoren beschikbaar. Hiermee kunnen de gegevens worden verwerkt in de knooppunten die de database hosten.

> [!NOTE]  
> Thrift wordt niet ondersteund door HBase in HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Gebruiksvoorbeelden voor Apache HBase

Het canonieke gebruiksvoorbeeld waarvoor BigTable (en door uitbreiding HBase) is gemaakt, waren webzoekopdrachten. Zoekmachines bouwen indexen die termen koppelen aan de webpagina's die deze termen bevatten. Er zijn echter tal van andere gebruiksvoorbeeld waarvoor HBase geschikt is. Enkele daarvan worden gespecificeerd in deze sectie.

|Scenario |Beschrijving |
|---|---|
|Sleutel-waardearchief|HBase kan worden gebruikt als een sleutel-waardearchief en is geschikt voor het beheren van berichtsystemen. Facebook gebruikt HBase voor het berichtensysteem. Daarnaast is HBase ideaal voor het opslaan en beheren van de communicatie via internet. WebTable gebruikt HBase om tabellen die worden geëxtraheerd uit webpagina's te zoeken en beheren.|
|Sensorgegevens|HBase is handig wanneer u gegevens wilt vastleggen die stapsgewijs uit diverse bronnen worden verzameld. Dit omvat onder andere sociale analyses en tijdreeksen. Andere voorbeelden zijn het up-to-date houden van interactieve dashboards met trends en prestatiemeteritems en het beheren van auditlogboeksystemen. Voorbeelden hiervan zijn de Bloomberg-handelsterminal en Open Time Series Database (OpenTSDB). OpenTSDB wordt gebruikt voor het opslaan en opvragen van verzamelde metrische gegevens over de status van serversystemen.|
|Realtime query|[Apache Phoenix](https://phoenix.apache.org/) is een SQL-query-engine voor Apache HBase. Deze is toegankelijk als een JDBC-stuurprogramma en maakt het uitvoeren van query's en beheren van HBase-tabellen SQL mogelijk.|
|HBase als een platform|Toepassingen kunnen worden uitgevoerd in HBase door HBase te gebruiken als gegevensopslag. Voorbeelden hiervan zijn Phoenix, OpenTSDB, `Kiji` en Titan. Toepassingen kunnen ook worden geïntegreerd met HBase. Voorbeelden zijn: [Apache Hive](https://hive.apache.org/), Apache Pig, [Solr](https://lucene.apache.org/solr/), Apache Storm, Apache Flume, [Apache Impala](https://impala.apache.org/), Apache Spark, `Ganglia` en Apache Drill.|

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Apache HBase en Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight-clusters maken in Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Apache HBase-replicatie in HDInsight configureren](apache-hbase-replication.md)
