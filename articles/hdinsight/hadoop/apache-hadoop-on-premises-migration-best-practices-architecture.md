---
title: 'Architectuur: on-premises Apache Hadoop naar Azure HDInsight'
description: Ontdek de aanbevolen procedures voor de architectuur voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 519dc53f6373ae1a9c8853d3fa90d137e9fa934b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435406"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>On-premises Apache Hadoop clusters migreren naar aanbevolen procedures voor de architectuur van Azure HDInsight

Dit artikel bevat aanbevelingen voor de architectuur van Azure HDInsight-systemen. Het is onderdeel van een serie die aanbevolen procedures biedt voor het migreren van on-premises Apache Hadoop systemen naar Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Meerdere door de werk belasting geoptimaliseerde clusters gebruiken

Veel on-premises Apache Hadoop implementaties bestaan uit één groot cluster dat veel werk belastingen ondersteunt. Dit ene cluster kan complex zijn en heeft mogelijk een inbreuk op de afzonderlijke services nodig om alles samen te werken. Voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight is een wijziging in de aanpak vereist.

Azure HDInsight-clusters zijn ontworpen voor een specifiek type Compute-gebruik. Omdat opslag kan worden gedeeld met meerdere clusters, is het mogelijk om meerdere door werk belasting geoptimaliseerde reken clusters te maken om te voldoen aan de behoeften van verschillende taken. Elk cluster type beschikt over de optimale configuratie voor die specifieke werk belasting. De volgende tabel bevat de ondersteunde cluster typen in HDInsight en de bijbehorende werk belastingen.

|Workload|HDInsight-cluster type|
|---|---|
|Batch verwerking (ETL/ELT)|Hadoop, Spark|
|Datawarehousing|Hadoop, Spark, interactieve query|
|IoT/streaming|Kafka, Storm, Spark|
|Transactionele verwerking van NoSQL|HBase|
|Interactieve en snellere query's met in-memory cache|Interactive Query|
|Gegevenswetenschap|ML Services, Spark|

In de volgende tabel ziet u de verschillende methoden die kunnen worden gebruikt voor het maken van een HDInsight-cluster.

|Hulpprogramma|Browser op basis|Opdrachtregel|REST-API|SDK|
|---|---|---|---|---|
|[Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI (ver 1,0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](/dotnet/api/overview/azure/hdinsight)||||X|
|[Python SDK](/python/api/overview/azure/hdinsight)||||X|
|[Java SDK](/java/api/overview/azure/hdinsight)||||X|
|[Azure Resource Manager-sjablonen](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Zie het artikel [cluster typen in HDInsight](../hadoop/apache-hadoop-introduction.md)voor meer informatie.

## <a name="use-transient-on-demand-clusters"></a>Tijdelijke on-demand clusters gebruiken

HDInsight-clusters kunnen lange tijd niet worden gebruikt. HDInsight biedt ondersteuning voor tijdelijke clusters op aanvraag, die kunnen worden verwijderd zodra de werk belasting is voltooid.

Wanneer u een cluster verwijdert, worden de bijbehorende opslag account en de externe meta gegevens niet verwijderd. Het cluster kan later opnieuw worden gemaakt met behulp van dezelfde opslag accounts en meta archieven.

Azure Data Factory kan worden gebruikt om het maken van HDInsight-clusters op aanvraag te plannen. Zie voor meer informatie het artikel [Apache Hadoop clusters op aanvraag maken in HDInsight met behulp van Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Opslag van Compute loskoppelen

Typische on-premises Hadoop-implementaties gebruiken dezelfde set machines voor gegevens opslag en gegevens verwerking. Omdat deze zijn gekoppeld, moeten Compute en opslag samen worden geschaald.

In HDInsight-clusters hoeft de opslag niet te worden gekoppeld aan Compute en kan deze zich bevinden in azure Storage, Azure Data Lake Storage of beide. Het ontkoppelen van opslag van Compute biedt de volgende voor delen:

- Gegevens delen tussen clusters.
- Het gebruik van tijdelijke clusters, aangezien de gegevens niet afhankelijk zijn van het cluster.
- Lagere opslag kosten.
- Opslag en berekening afzonderlijk schalen.
- Gegevens replicatie tussen regio's.

Er worden reken clusters gemaakt dicht bij de resources van het opslag account in een Azure-regio om de prestatie kosten voor het scheiden van Compute en opslag te verminderen. Hoge-snelheids netwerken maken het efficiënt voor de reken knooppunten voor toegang tot de gegevens in azure Storage.

## <a name="use-external-metadata-stores"></a>Externe metagegevensopslag gebruiken

Er zijn twee belang rijke meta Stores die werken met HDInsight-clusters: [Apache Hive](https://hive.apache.org/) en [Apache Oozie](https://oozie.apache.org/). De Hive-metastore is de centrale schema opslagplaats die kan worden gebruikt door engines voor gegevens verwerking, waaronder Hadoop, Spark, LLAP, Presto en Apache varken. In de Oozie-meta Store worden gegevens over de planning en de status van in uitvoering en voltooide Hadoop-taken opgeslagen.

HDInsight maakt gebruik van Azure SQL Database voor Hive-en Oozie-meta Stores. Er zijn twee manieren om een meta Store in HDInsight-clusters in te stellen:

1. Standaard-META Store

    - Geen extra kosten.
    - De meta Store wordt verwijderd wanneer het cluster wordt verwijderd.
    - De meta Store kan niet worden gedeeld tussen verschillende clusters.
    - Maakt gebruik van basis-Azure SQL DB, die een vijf DTU-limiet heeft.

1. Aangepaste externe meta Store

    - Geef een externe Azure SQL Database op als meta Store.
    - Clusters kunnen worden gemaakt en verwijderd zonder dat meta gegevens verloren gaan, inclusief Oozie-taak Details van het Hive-schema.
    - Eén meta store-data base kan worden gedeeld met verschillende typen clusters.
    - Meta Store kan naar behoefte worden geschaald.
    - Zie [externe meta gegevens archieven gebruiken in azure HDInsight](../hdinsight-use-external-metadata-stores.md)voor meer informatie.

## <a name="best-practices-for-hive-metastore"></a>Aanbevolen procedures voor Hive-meta Store

Enkele HDInsight-Hive-metastore best practices zijn als volgt:

- Gebruik een aangepaste externe meta Store om reken resources en meta gegevens te scheiden.
- Begin met een Azure SQL-exemplaar van de S2-laag, die 50 DTU en 250 GB opslag biedt. Als er een knel punt wordt weer geven, kunt u de data base omhoog schalen.
- Deel de meta Store die is gemaakt voor één HDInsight-cluster versie niet met clusters van een andere versie. Verschillende Hive-versies gebruiken verschillende schema's. Een meta Store kan bijvoorbeeld niet worden gedeeld met zowel Hive 1,2-als hive 2,1-clusters.
- Maak regel matig een back-up van de aangepaste meta Store.
- Behoud de meta Store en het HDInsight-cluster in dezelfde regio.
- Bewaak de meta Store voor prestaties en beschik baarheid met behulp van Azure SQL Database controle hulpprogramma's, zoals Azure Portal of Azure Monitor Logboeken.
- Voer de `ANALYZE TABLE` opdracht uit zoals vereist voor het genereren van statistieken voor tabellen en kolommen. Bijvoorbeeld `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Aanbevolen procedures voor verschillende werk belastingen

- Overweeg het gebruik van LLAP-cluster voor interactieve Hive-query's met verbeterde reactie tijd [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) is een nieuwe functie in hive 2,0 waarmee query's in het geheugen worden opgeslagen in de cache. LLAP maakt Hive-query's veel sneller, tot [26x sneller dan Hive 1. x in sommige gevallen](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Overweeg het gebruik van Spark-taken in plaats van Hive-taken.
- Overweeg op Impala gebaseerde query's te vervangen door LLAP-query's.
- Overweeg MapReduce-taken te vervangen door Spark-taken.
- Overweeg om Spark-batch taken met lage latentie te vervangen met behulp van Spark Structured streaming-taken.
- Overweeg het gebruik van Azure Data Factory (ADF) 2,0 voor het delen van gegevens.
- Overweeg Ambari voor cluster beheer.
- Wijzig de gegevens opslag van on-premises HDFS naar WASB of ADLS of ADFS voor het verwerken van scripts.
- U kunt zwerver RBAC gebruiken in Hive-tabellen en-controles.
- Overweeg het gebruik van CosmosDB in plaats van MongoDB of Cassandra.

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel in deze serie:

- [Aanbevolen procedures voor de infra structuur voor on-premises migratie van Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)