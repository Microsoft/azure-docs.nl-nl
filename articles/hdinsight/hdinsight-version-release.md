---
title: Overzicht van HDInsight 4,0-Azure
description: HDInsight 3.6 vergelijken met functies, beperkingen en aanbevelingen voor upgrades van HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 2fd7a3e512b79651fdcf6a6ac0c14822361fc263
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350191"
---
# <a name="azure-hdinsight-40-overview"></a>Overzicht van Azure HDInsight 4,0

Azure HDInsight is een van de populairste services van zakelijke klanten voor Apache Hadoop en Apache Spark. HDInsight 4,0 is een Cloud distributie van Apache Hadoop-onderdelen. Dit artikel bevat informatie over de meest recente Azure HDInsight-release en over hoe u moet upgraden.

## <a name="whats-new-in-hdinsight-40"></a>Wat is er nieuw in HDInsight 4,0?

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Analytische verwerking van Apache Hive 3,0 en lage latentie

Apache Hive LLAP (Low-latentie Analytical Processing) gebruikt permanente query servers en caching in het geheugen. Dit proces levert snelle SQL-query resultaten voor gegevens in externe Cloud opslag. Hive LLAP maakt gebruik van een set permanente daemons die fragmenten van Hive-query's uitvoeren. Query's uitvoeren in LLAP is vergelijkbaar met Hive zonder LLAP, maar werkroltaken worden in LLAP-daemons uitgevoerd in plaats van in containers.

Voordelen van LLAP in Hive zijn:

* De mogelijkheid om diepere SQL-analyses uit te voeren zonder dat de prestaties en de aanpassing worden geofferd. Zoals complexe samen voegingen, subquery's, functies in Vensters, sorteren, door de gebruiker gedefinieerde functies en complexe aggregaties.

* Interactieve query's op gegevens in dezelfde opslag waar de gegevens worden voorbereid, zodat u voor analytische verwerking geen gegevens hoeft te verplaatsen van een opslag naar een andere engine.

* Als query resultaten in de cache worden opgeslagen, kunnen eerder berekende query resultaten opnieuw worden gebruikt. Deze cache bespaart tijd en resources die zijn besteed aan het uitvoeren van de cluster taken die vereist zijn voor de query.

### <a name="hive-dynamic-materialized-views"></a>Dynamisch gerealiseerde weergaven in Hive

Onderdeel ondersteunt nu dynamische gerealiseerde weer gaven of de voor berekeningen van relevante samen vattingen. De weer gaven versnellen de query verwerking in data warehouses. Gerealiseerde weergaven worden in Hive op het systeem bewaard en gebruiken naadloos LLAP-versnelling.

### <a name="hive-transactional-tables"></a>Transactionele Hive-tabellen

HDI 4,0 bevat Apache Hive 3. Hive 3 vereist atomische, consistentie, isolatie en duurzaamheids compatibiliteit voor transactionele tabellen die in het Hive-Warehouse wonen. ACID-compatibele tabellen en tabelgegevens worden via Hive geopend en beheerd. Gegevens in tabellen voor maken, ophalen, bijwerken en verwijderen moeten zich in de ORC-bestands indeling (Optimized Row column) bevinden. Alleen-invoegen tabellen ondersteunen alle bestands indelingen.

* ACID v2 heeft prestatieverbeteringen in zowel het opslagformaat als de engine voor uitvoerbewerkingen.

* ACID is standaard ingeschakeld voor ondersteuning voor alle gegevensupdates.

* Met verbeterde ACID-mogelijkheden kunt u op rijniveau bijwerken en verwijderen.

* Er is geen prestatieoverhead.

* Er is geen bucketing vereist.

* Spark kan naar ACID-tabellen in Hive lezen en schrijven via Hive Warehouse Connector.

Meer informatie over [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Apache Spark ontvang bij te werken tabellen en ACID-transacties via de Hive Warehouse Connector. Met Hive Warehouse Connector registreert u transactionele Hive-tabellen als externe tabellen in Spark voor volledige transactionele functionaliteit. Eerdere versies ondersteunden alleen manipulatie van partitietabellen. Hive Warehouse connector biedt ook ondersteuning voor streaming DataFrames.  Dit proces streamt Lees-en schrijf bewerkingen naar de Hive-tabellen van transactionele en streaming vanuit Spark.

Spark-executors maken rechtstreeks verbinding met LLAP-daemons van Hive om gegevens op een transactionele manier op te halen en bij te werken, waardoor Hive de controle houdt over de gegevens.

Apache Spark in HDInsight 4.0 ondersteunt de volgende scenario's:

* Training voor machine learning-modellen uitvoeren voor hetzelfde transactionele tabel dat wordt gebruikt voor rapportage.
* ACID-transacties gebruiken om kolommen van Spark ML veilig toe te voegen aan een Hive-tabel.
* Een Spark-streamingtaak uitvoeren in de wijzigingsfeed van een streaming Hive-tabel.
* ORC-bestanden rechtstreeks vanuit een Spark Structured Streaming-taak maken.

U hoeft zich geen zorgen meer te maken over per ongeluk toegang tot de Hive-transactionele tabellen rechtstreeks vanuit Spark. Resulteert in inconsistente resultaten, dubbele gegevens of beschadiging van gegevens. In HDInsight 4,0 worden Spark-tabellen en Hive-tabellen opgeslagen in afzonderlijke meta Stores. Gebruik de Hive Data Warehouse Connector om transactionele Hive-tabellen expliciet te registreren als externe Spark-tabellen.

Meer informatie over [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 is opgenomen in HDI 4.0 met de volgende wijzigingen:

* Oozie voert Hive-acties niet langer uit. De Hive CLI is verwijderd en vervangen door BeeLine.

* U sluit ongewenste afhankelijkheden uit van ShareLib door een uitsluitpatroon op te nemen in het bestand **job.properties**.

Meer informatie over [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Upgraden naar HDInsight 4,0

Test uw onderdelen grondig voordat u de nieuwste versie in een productie omgeving implementeert. HDInsight 4,0 is beschikbaar om het upgrade proces te starten. HDInsight 3,6 is de standaard optie om onbedoelde Mishaps te voor komen.

Er wordt geen ondersteund upgradepad van eerdere versies van HDInsight naar HDInsight 4,0. Omdat de indelingen van meta Store en BLOB-gegevens zijn gewijzigd, is 4,0 niet compatibel met eerdere versies. Het is belang rijk dat u uw nieuwe HDInsight 4,0-omgeving gescheiden blijft van uw huidige productie omgeving. Als u HDInsight 4,0 op uw huidige omgeving implementeert, wordt uw meta Store definitief bijgewerkt.  

## <a name="limitations"></a>Beperkingen

* HDInsight 4,0 biedt geen ondersteuning voor MapReduce voor Apache Hive. Gebruik in plaats daarvan Apache Tez. Meer informatie over [Apache Tez](https://tez.apache.org/).
* HDInsight 4,0 biedt geen ondersteuning voor Apache Storm.
* HDInsight 4,0 biedt geen ondersteuning voor het cluster type van de MILLILITERs Services.
* De Hive-weer gave is alleen beschikbaar op HDInsight 4,0-clusters met een versie nummer dat gelijk is aan of groter is dan 4,1. Dit versie nummer is beschikbaar in Ambari admin-> versies.
* Shell-interpreter in Apache Zeppelin wordt niet ondersteund in Spark-en interactieve query clusters.
* U kunt LLAP in een Apache Spark-LLAP-cluster niet *uitschakelen*. U kunt alleen LLAP uitschakelen.
* Azure Data Lake Storage Gen2 kunt geen Jupyter-notebooks opslaan in een Spark-cluster.
* Apache-Pig wordt standaard uitgevoerd op TEZ, maar u kunt dit echter wijzigen in MapReduce
* De integratie van Spark SQL zwerver voor rij-en kolom beveiliging is afgeschaft
* Spark 2,4 en Kafka 2,1 zijn beschikbaar in HDInsight 4,0, dus Spark 2,3 en Kafka 1,1 worden niet meer ondersteund. U kunt het beste Spark 2,4 & Kafka 2,1 en hoger in HDInsight 4,0 gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [HBase-migratie handleiding](./hbase/apache-hbase-migrate-new-version.md)
* [Hand leiding voor Hive-migratie](./interactive-query/apache-hive-migrate-workloads.md)
* [Kafka-migratie handleiding](./kafka/migrate-versions.md)
* [Spark-migratie handleiding](./spark/migrate-versions.md)
* [Documentatie voor Azure HDInsight](index.yml)
* [Release opmerkingen](hdinsight-release-notes.md)