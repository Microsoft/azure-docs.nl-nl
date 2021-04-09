---
title: Apache Spark richtlijnen voor Azure HDInsight
description: Meer informatie over richt lijnen voor het gebruik van Apache Spark in azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: f81435abbedec25cb8abe0b958065a493c15ce4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938804"
---
# <a name="apache-spark-guidelines"></a>Apache Spark richtlijnen

Dit artikel bevat verschillende richt lijnen voor het gebruik van Apache Spark in azure HDInsight.

## <a name="how-do-i-run-or-submit-spark-jobs"></a>Hoe kan ik u Spark-taken uitvoeren of verzenden?

| Optie | Documenten |
|---|---|
| VSCode | [Gebruik de Spark-& Hive-Hulpprogram Ma's voor Visual Studio code](../hdinsight-for-vscode.md) |
| Jupyter Notebooks | [Zelfstudie: Gegevens laden en query's uitvoeren in een Apache Spark-cluster in Azure HDInsight](./apache-spark-load-data-run-query.md) |
| IntelliJ | [Zelf studie: Azure-toolkit voor IntelliJ gebruiken om Apache Spark-toepassingen voor een HDInsight-cluster te maken](./apache-spark-intellij-tool-plugin.md) |
| IntelliJ | [Zelfstudie: Een Scala Maven-toepassing maken voor Apache Spark in HDInsight met behulp van IntelliJ](./apache-spark-create-standalone-application.md) |
| Zeppelin-notebooks | [Apache Zeppelin-notebooks gebruiken met Apache Spark-cluster in Azure HDInsight](./apache-spark-zeppelin-notebook.md) |
| Externe taak verzenden met livy | [Apache Spark REST API gebruiken voor het verzenden van externe taken naar een HDInsight Spark-cluster](./apache-spark-livy-rest-interface.md) |
|[Apache Oozie](../hdinsight-use-oozie-linux-mac.md)|Oozie is een werk stroom-en coördinatie systeem waarmee Hadoop-taken worden beheerd.|
|[Apache Livy](./apache-spark-livy-rest-interface.md)|U kunt livy gebruiken om interactieve Spark-shells uit te voeren of om batch taken te verzenden die moeten worden uitgevoerd op Spark.|
|[Azure Data Factory voor Apache Spark](../../data-factory/transform-data-using-spark.md)|De Spark-activiteit in een Data Factory pijp lijn voert een Spark-programma uit op uw eigen of [HDInsight-cluster op aanvraag.|
|[Azure Data Factory voor Apache Hive](../../data-factory/transform-data-using-hadoop-hive.md)|De HDInsight Hive-activiteit in een Data Factory pijp lijn voert Hive-query's uit op uw eigen of HDInsight-cluster op aanvraag.|

## <a name="how-do-i-monitor-and-debug-spark-jobs"></a>Hoe kan ik controleren en fouten opsporen in Spark-taken?

| Optie | Documenten |
|---|---|
| Azure-toolkit voor IntelliJ | [Fout bij het opsporen van Spark-taken met Azure-toolkit voor IntelliJ (preview-versie)](apache-spark-intellij-tool-failure-debug.md) |
| Azure-toolkit voor IntelliJ via SSH | [Lokaal of op afstand fouten opsporen in Apache Spark-toepassingen in een HDInsight-cluster met de Azure-toolkit voor IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md) |
| Azure-toolkit voor IntelliJ via VPN | [Azure-toolkit voor IntelliJ gebruiken om fout opsporing uit te Apache Spark toepassingen op afstand in HDInsight via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) |
| Taak grafiek op Apache Spark geschiedenis server | [Uitgebreide Apache Spark History Server gebruiken om fouten op te sporen in Apache Spark-toepassingen](./apache-azure-spark-history-server.md) |

## <a name="how-do-i-make-my-spark-jobs-run-more-efficiently"></a>Hoe kan ik mijn Spark-taken efficiënter uit te voeren?

| Optie | Documenten |
|---|---|
| I/o-cache | [Verbeter de prestaties van Apache Spark werk belastingen met behulp van de Azure HDInsight IO-cache (preview)](./apache-spark-improve-performance-iocache.md) |
| Configuratie-opties | [Apache Spark-taken optimaliseren](./apache-spark-perf.md) |

## <a name="how-do-i-connect-to-other-azure-services"></a>Hoe kan ik verbinding maken met andere Azure-Services?

| Optie | Documenten |
|---|---|
| Apache Hive op HDInsight | [Apache Spark en Apache Hive integreren met Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md) |
| Apache HBase in HDInsight | [Apache Spark gebruiken om Apache HBase-gegevens te lezen en schrijven](../hdinsight-using-spark-query-hbase.md) |
| Apache Kafka op HDInsight | [Zelfstudie: Apache Spark Structured Streaming gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md) |
| Azure Cosmos DB | [Azure Synapse Link voor Azure Cosmos DB](../../cosmos-db/synapse-link.md) |

## <a name="what-are-my-storage-options"></a>Wat zijn mijn opslag opties?

| Optie | Documenten |
|---|---|
| Azure Data Lake Storage Gen2 | [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md) |
| Azure Data Lake Storage Gen1 | [Azure Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen1.md) |
| Azure Blob Storage | [Azure-opslag gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-blob-storage.md) |

## <a name="next-steps"></a>Volgende stappen

* [Apache Spark-instellingen configureren](apache-spark-settings.md)
* [Apache Spark-taken in HDInsight optimaliseren](apache-spark-perf.md)
