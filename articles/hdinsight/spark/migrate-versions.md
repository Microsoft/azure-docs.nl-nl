---
title: Apache Spark 2,1-of 2,2-workloads migreren naar 2,3 of 2,4-Azure HDInsight
description: Meer informatie over het migreren van Apache Spark 2,1 en 2,2 naar 2,3 of 2,4.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: af1894d2f63357006e87fa8e4533f135ecc02f21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944755"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>Apache Spark 2,1-en 2,2-workloads migreren naar 2,3 en 2,4

In dit document wordt uitgelegd hoe u Apache Spark workloads op Spark 2,1 en 2,2 migreert naar 2,3 of 2,4.

Zoals besproken in de [release opmerkingen](../hdinsight-release-notes.md#upcoming-changes), vanaf 1 juli 2020, worden de volgende cluster configuraties niet ondersteund en kunnen klanten geen nieuwe clusters maken met deze configuraties:
 - Spark 2,1 en 2,2 in een HDInsight 3,6 Spark-cluster
 - Spark 2,3 in een HDInsight 4,0 Spark-cluster

Bestaande clusters in deze configuraties worden uitgevoerd zonder ondersteuning van micro soft. Als u met Spark 2,1 of 2,2 op HDInsight 3,6 werkt, gaat u naar Spark 2,3 op HDInsight 3,6 van juni 30 2020 om mogelijke onderbreking van systeem/ondersteuning te voor komen. Als u met Spark 2,3 werkt op een HDInsight 4,0-cluster, gaat u naar Spark 2,4 op HDInsight 4,0 van juni 30 2020 om mogelijke onderbreking van systeem/ondersteuning te voor komen.

Zie [hdinsight-cluster migreren naar een nieuwere versie](../hdinsight-upgrade-cluster.md)voor algemene informatie over het migreren van een hdinsight-cluster van 3,6 naar 4,0. Zie [Apache Spark: Versioning Policy](https://spark.apache.org/versioning-policy.html)(Engelstalig) voor algemene informatie over het migreren naar een nieuwere versie van Apache Spark.

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>Richt lijnen voor upgrades van Spark-versies op HDInsight

| Upgradescenario | Mechanisme | Aandachtspunten | Integratie met Spark/Hive |
|------------------|-----------|--------------------|------------------------|
|HDInsight 3,6 Spark 2,1 naar HDInsight 3,6 Spark 2,3| Clusters opnieuw maken met HDInsight Spark 2,3 | Bekijk de volgende artikelen: <br> [Apache Spark: upgraden van Spark SQL 2,2 naar 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: upgraden van Spark SQL 2,1 naar 2,2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Geen wijziging |
|HDInsight 3,6 Spark 2,2 naar HDInsight 3,6 Spark 2,3 | Clusters opnieuw maken met HDInsight Spark 2,3 | Bekijk de volgende artikelen: <br> [Apache Spark: upgraden van Spark SQL 2,2 naar 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Geen wijziging |
| HDInsight 3,6 Spark 2,1 naar HDInsight 4,0 Spark 2,4 | Clusters opnieuw maken met HDInsight 4,0 Spark 2,4 | Bekijk de volgende artikelen: <br> [Apache Spark: upgraden van Spark SQL 2,3 naar 2,4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: upgraden van Spark SQL 2,2 naar 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: upgraden van Spark SQL 2,1 naar 2,2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | De Spark-en Hive-integratie is gewijzigd in HDInsight 4,0. <br><br> In HDInsight 4,0 gebruiken Spark en Hive onafhankelijke catalogi om toegang te krijgen tot SparkSQL of Hive-tabellen. Een tabel gemaakt door Spark in de Spark-catalogus. Een tabel die door Hive is gemaakt, bevindt zich in de Hive-catalogus. Dit gedrag wijkt af van HDInsight 3,6 waarbij een gemeen schappelijke catalogus van Hive en Spark wordt gedeeld. De Hive-en Spark-integratie in HDInsight 4,0 is afhankelijk van Hive Warehouse connector (HWC). HWC werkt als een brug tussen Spark en Hive. Meer informatie over Hive Warehouse connector. <br> Als u in HDInsight 4,0 de meta Store tussen Hive en Spark wilt delen, kunt u dit doen door de Property Store. catalog. default in te wijzigen in Hive in uw Spark-cluster. U kunt deze eigenschap vinden in Ambari Advanced spark2-component-site-override. Het is belang rijk om te begrijpen dat het delen van de meta Store alleen werkt voor externe Hive-tabellen. dit werkt niet als u interne/beheerde Hive-tabellen of zuur tabellen hebt. <br><br>Lees de [werk belasting van Azure HDInsight 3,6-componenten migreren naar HDInsight 4,0](../interactive-query/apache-hive-migrate-workloads.md) voor meer informatie.<br><br> |
| HDInsight 3,6 Spark 2,2 naar HDInsight 4,0 Spark 2,4 | Clusters opnieuw maken met HDInsight 4,0 Spark 2,4 | Bekijk de volgende artikelen: <br> [Apache Spark: upgraden van Spark SQL 2,3 naar 2,4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: upgraden van Spark SQL 2,2 naar 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | De Spark-en Hive-integratie is gewijzigd in HDInsight 4,0. <br><br> In HDInsight 4,0 gebruiken Spark en Hive onafhankelijke catalogi om toegang te krijgen tot SparkSQL of Hive-tabellen. Een tabel gemaakt door Spark in de Spark-catalogus. Een tabel die door Hive is gemaakt, bevindt zich in de Hive-catalogus. Dit gedrag wijkt af van HDInsight 3,6 waarbij een gemeen schappelijke catalogus van Hive en Spark wordt gedeeld. De Hive-en Spark-integratie in HDInsight 4,0 is afhankelijk van Hive Warehouse connector (HWC). HWC werkt als een brug tussen Spark en Hive. Meer informatie over Hive Warehouse connector. <br> Als u in HDInsight 4,0 de meta Store tussen Hive en Spark wilt delen, kunt u dit doen door de Property Store. catalog. default in te wijzigen in Hive in uw Spark-cluster. U kunt deze eigenschap vinden in Ambari Advanced spark2-component-site-override. Het is belang rijk om te begrijpen dat het delen van de meta Store alleen werkt voor externe Hive-tabellen. dit werkt niet als u interne/beheerde Hive-tabellen of zuur tabellen hebt. <br><br>Lees de [werk belasting van Azure HDInsight 3,6-componenten migreren naar HDInsight 4,0](../interactive-query/apache-hive-migrate-workloads.md) voor meer informatie.|

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-cluster migreren naar een nieuwere versie](../hdinsight-upgrade-cluster.md)
* [Azure HDInsight 3,6 Hive-workloads migreren naar HDInsight 4,0](../interactive-query/apache-hive-migrate-workloads.md)
