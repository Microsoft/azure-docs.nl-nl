---
title: RequestBodyTooLarge-fout van Apache Spark app-Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge wordt weer gegeven in het logboek voor Apache Spark streaming-app in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 73ae646cb083841ee1d55b6c7ce6af7180cef08e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929423"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge ' worden weer gegeven in Apache Spark streaming-app-logboek in HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De fout: `NativeAzureFileSystem ... RequestBodyTooLarge` wordt weer gegeven in het stuur programma-logboek voor een Apache Spark streaming-app.

## <a name="cause"></a>Oorzaak

Het Spark-gebeurtenis logboek bestand heeft waarschijnlijk de maximale bestands lengte voor WASB.

In Spark 2,3 genereert elke Spark-app één Spark-gebeurtenis logboek bestand. Het Spark-gebeurtenis logboek bestand voor een Spark-streaming-app blijft groeien terwijl de app wordt uitgevoerd. Een bestand op WASB heeft nu een limiet van 50000 MB en de standaard blok grootte is 4 Mega bytes. In de standaard configuratie is de maximale bestands grootte 195 GB. Azure Storage heeft de maximale blok grootte echter verhoogd tot 100 MB, waardoor de limiet voor de afzonderlijke bestanden effectief wordt ingesteld op 4,75 TB. Zie [Schaalbaarheids- en prestatiedoelen voor blob-opslag](../../storage/blobs/scalability-targets.md) voor meer informatie.

## <a name="resolution"></a>Oplossing

Er zijn drie oplossingen beschikbaar voor deze fout:

* Verg root de blok grootte tot 100 MB. Wijzig in de gebruikers interface van Ambari de eigenschap HDFS Configuration `fs.azure.write.request.size` (of maak deze in de `Custom core-site` sectie). Stel de eigenschap in op een hogere waarde, bijvoorbeeld: 33554432. Sla de bijgewerkte configuratie op en start de betrokken onderdelen opnieuw op.

* Stop de taak Spark-streaming regel matig en verzend deze opnieuw.

* Gebruik HDFS om Spark-gebeurtenis logboeken op te slaan. Het gebruik van HDFS voor opslag kan leiden tot verlies van Spark-gebeurtenis gegevens tijdens het schalen van clusters of Azure-upgrades.

    1. Wijzigingen aanbrengen in `spark.eventlog.dir` en `spark.history.fs.logDirectory` via de Ambari-gebruikers interface:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Mappen maken op HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Start alle betrokken services opnieuw via de Ambari-gebruikers interface.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]