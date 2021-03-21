---
title: Wat is Interactive Query in Azure HDInsight?
description: Een inleiding tot Interactive Query, ook wel Apache Hive LLAP genoemd, in Azure HDInsight
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 2813554700e015c0ac34e47d632d16d97c948c4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98941073"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Wat is Interactive Query in Azure HDInsight?

Interactive Query (ook wel Apache Hive LLAP genoemd, of [analyse met lage latentie](https://cwiki.apache.org/confluence/display/Hive/LLAP)) is een Azure HDInsight-[clustertype](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type). Interactive Query biedt ondersteuning voor caching in het geheugen, waardoor Apache Hive query's sneller en veel meer interactief worden uitgevoerd. Klanten gebruiken Interactive Query om zeer snel gegevens op te vragen die zijn opgeslagen in Azure Storage en Azure Data Lake Storage. Met Interactive Query kunnen ontwikkelaars en gegevenswetenschappers eenvoudig samenwerken aan big data met behulp van de BI-tools die ze het liefst gebruiken. HDInsight Interactive Query ondersteunt diverse hulpprogramma's waarmee u op eenvoudige wijze toegang kunt krijgen tot big data.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Een Interactive Query-cluster is anders dan een Apache Hadoop-cluster. Het bevat alleen de Hive-service.

U hebt alleen toegang tot de Hive-service in het Interactive Query-cluster via Apache Ambari Hive View, Beeline en het Microsoft Hive Open Database Connectivity-stuurprogramma (Hive ODBC). U hebt geen toegang via de Hive-console, Templeton, de klassieke Azure-CLI of Azure PowerShell.

## <a name="create-an-interactive-query-cluster"></a>Een Interactive Query-cluster maken

Zie [Apache Hadoop-clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie over het maken van HDInsight-clusters. Kies het clustertype Interactive Query.

> [!IMPORTANT]
> De minimale grootte van het hoofdknooppunt voor Interactive Query-clusters is Standard_D13_v2. Zie het [overzicht van Azure-VM-grootten](../../cloud-services/cloud-services-sizes-specs.md#dv2-series) voor meer informatie.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Apache Hive-query's uitvoeren vanuit Interactive Query

Als u Hive-query's wilt uitvoeren, hebt u de volgende opties:

|Methode |Beschrijving |
|---|---|
|Microsoft Power BI|Zie [Visualize Interactive Query Apache Hive data with Power BI in Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) (Apache Hive-gegevens uit Interactive Query visualiseren met Power BI in Azure HDInsight) en [Visualize big data with Power BI in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md) (Big data visualiseren met Power BI in Azure HDInsight).|
|Visual Studio|Zie [Verbinding maken met Azure HDInsight en Apache Hive-query's uitvoeren met behulp van Data Lake Tools voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).|
|Visual Studio Code|Zie [Visual Studio code gebruiken voor Apache Hive, LLAP of pySpark](../hdinsight-for-vscode.md).|
|Apache Ambari Hive-weergave|Zie [Apache Hive-weergave gebruiken met Apache Hadoop in HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md). Hive-weergave is niet beschikbaar in HDInsight 4.0.|
|Apache Beeline|Zie [Apache Hive gebruiken met Apache Hadoop in HDInsight met Beeline](../hadoop/apache-hadoop-use-hive-beeline.md). U kunt Beeline gebruiken vanuit het hoofdknooppunt of vanuit een leeg edge-knooppunt. We raden u aan Beeline te gebruiken vanaf een leeg edge-knooppunt. Zie [Lege edge-knooppunten gebruiken in HDInsight](../hdinsight-apps-use-edge-node.md) voor meer informatie over het maken van een HDInsight-cluster met behulp van een leeg edge-knooppunt.|
|Hive ODBC|Zie [Excel koppelen aan Apache Hadoop met behulp van het Hive ODBC-stuurprogramma van Microsoft](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).|

De verbindingsreeks voor Java Database Connectivity (JDBC) zoeken:

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`, waarbij `CLUSTERNAME` de naam van uw cluster is.
1. Selecteer het klembordpictogram om de URL naar het klembord te kopiëren:

   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van Interactive Query-clusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Meer informatie over [Interactive Query Hive-gegevens visualiseren met Power BI in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Meer informatie over [Apache Zeppelin gebruiken voor het uitvoeren van Apache Hive-query's in Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
