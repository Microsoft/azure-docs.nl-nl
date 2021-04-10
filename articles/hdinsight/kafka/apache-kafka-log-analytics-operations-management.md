---
title: Azure Monitor logboeken voor Apache Kafka-Azure HDInsight
description: Informatie over het gebruik van Azure Monitor logboeken voor het analyseren van logboeken van Apache Kafka cluster op Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 74db121c33864ee72ad984b49c8fa43afa0f598c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870514"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Logboeken analyseren voor Apache Kafka in HDInsight

Informatie over het gebruik van Azure Monitor logboeken voor het analyseren van logboeken die zijn gegenereerd door Apache Kafka op HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Logboek locatie

Apache Kafka-Logboeken in het cluster bevinden zich op `/var/log/kafka` . Kafka-logboeken worden niet opgeslagen of bewaard in de levens cyclus van het cluster, ongeacht of beheerde schijven worden gebruikt. De volgende tabel bevat de beschik bare Logboeken.

|Logboek |Description |
|---|---|
|Kafka. out|stdout en stderr van het Kafka-proces. U vindt de opstart-en afsluit logboeken van Kafka in dit bestand.|
|server. log|Het belangrijkste Kafka-server logboek. Alle Kafka Broker-logboeken worden hier beëindigd.|
|controller. log|Controller Logboeken als de Broker fungeert als controller.|
|statechange. log|Alle status wijzigings gebeurtenissen voor Brokers worden vastgelegd in dit bestand.|
|Kafka-GC. log|Statistieken voor Kafka-garbagecollection.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Azure Monitor logboeken inschakelen voor Apache Kafka

De stappen voor het inschakelen van Azure Monitor logboeken voor HDInsight zijn hetzelfde voor alle HDInsight-clusters. Gebruik de volgende koppelingen om inzicht te krijgen in het maken en configureren van de vereiste services:

1. Maak een Log Analytics-werkruimte. Zie de [Logboeken in azure monitor](../../azure-monitor/logs/data-platform-logs.md) document voor meer informatie.

2. Maak een Kafka in HDInsight-cluster. Zie het document [beginnen met Apache Kafka in HDInsight](apache-kafka-get-started.md) voor meer informatie.

3. Configureer het Kafka-cluster om Azure Monitor-logboeken te gebruiken. Zie [Azure monitor Logboeken gebruiken om HDInsight-documenten te controleren](../hdinsight-hadoop-oms-log-analytics-tutorial.md) voor meer informatie.

> [!IMPORTANT]  
> Het kan ongeveer 20 minuten duren voordat er gegevens beschikbaar zijn voor Azure Monitor Logboeken.

## <a name="query-logs"></a>Query-logboeken

1. Selecteer uw Log Analytics-werk ruimte in de [Azure Portal](https://portal.azure.com).

2. Ga naar het menu links en selecteer **Logboeken** onder **Algemeen**. Hier kunt u zoeken naar gegevens die zijn verzameld uit Kafka. Voer een query in het query venster in en selecteer vervolgens **uitvoeren**. Hier volgen enkele voor beelden van zoek opdrachten:

* Schijf gebruik:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* CPU-gebruik:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Inkomende berichten per seconde: (Vervang door `your_kafka_cluster_name` de naam van uw cluster.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Binnenkomende bytes per seconde: (vervangen `wn0-kafka` door de hostnaam van het worker-knoop punt.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Uitgaande bytes per seconde: (vervangen door `your_kafka_cluster_name` de naam van uw cluster.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    U kunt ook invoeren `*` om te zoeken naar alle typen die zijn geregistreerd. Momenteel zijn de volgende logboeken beschikbaar voor query's:

    | Logboek type | Description |
    | ---- | ---- |
    | Log \_ kafkaserver- \_ LC | Kafka Broker-server. log |
    | Log \_ kafkacontroller- \_ LC | Kafka Broker controller. log |
    | metrische gegevens \_ Kafka \_ cl | Kafka JMX-metrische gegevens |

    :::image type="content" source="./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png" alt-text="CPU-gebruik voor Apache Kafka log Analytics" border="true":::

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure monitor](../../azure-monitor/overview.md)voor meer informatie over Azure monitor en [query Azure monitor logboeken voor het bewaken van HDInsight-clusters](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Raadpleeg de volgende documenten voor meer informatie over het werken met Apache Kafka:

* [Mirror Apache Kafka tussen HDInsight-clusters](apache-kafka-mirroring.md)
* [De schaal van Apache Kafka op HDInsight verg Roten](apache-kafka-scalability.md)
* [Apache Spark streaming (DStreams) gebruiken met Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Apache Spark Structured streamen gebruiken met Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)