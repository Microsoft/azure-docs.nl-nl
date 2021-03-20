---
title: Wat is Apache Hive en HiveQL-Azure HDInsight
description: Apache Hive is een Data Warehouse systeem voor Apache Hadoop. U kunt een query uitvoeren op gegevens die zijn opgeslagen in Hive met behulp van HiveQL, die vergelijkbaar zijn met Transact-SQL. In dit document leest u hoe u Hive en HiveQL kunt gebruiken met Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: 4e8c6b25055dfc38d56509e1744b8c7fcac40700
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944296"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Wat is Apache Hive en HiveQL in azure HDInsight?

[Apache Hive](https://hive.apache.org/) is een Data Warehouse systeem voor Apache Hadoop. Hive maakt gegevens samenvatting, query's en analyses van gegevens mogelijk. Hive-query's worden geschreven in HiveQL. Dit is een query taal die vergelijkbaar is met SQL.

Met hive kunt u de structuur projecteren op grotendeels ongestructureerde gegevens. Nadat u de structuur hebt gedefinieerd, kunt u HiveQL gebruiken om de gegevens op te vragen zonder kennis van Java of MapReduce.

HDInsight biedt verschillende cluster typen, die zijn afgestemd op specifieke werk belastingen. De volgende cluster typen worden meestal gebruikt voor Hive-query's:

|Clustertype |Beschrijving|
|---|---|
|Interactive Query|Een Hadoop-cluster dat [LLAP-functionaliteit (Low latentie Analytical Processing)](https://cwiki.apache.org/confluence/display/Hive/LLAP) biedt om de reactie tijden voor interactieve query's te verbeteren. Zie het document [Start with Interactive query in HDInsight](../interactive-query/apache-interactive-query-get-started.md) voor meer informatie.|
|Hadoop|Een Hadoop-cluster dat is afgestemd op werk belastingen voor batch verwerking. Zie het document [beginnen met Apache Hadoop in HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) voor meer informatie.|
|Spark|Apache Spark heeft ingebouwde functionaliteit voor het werken met Hive. Zie het document [beginnen met Apache Spark in HDInsight](../spark/apache-spark-jupyter-spark-sql.md) voor meer informatie.|
|HBase|HiveQL kan worden gebruikt voor het opvragen van gegevens die zijn opgeslagen in Apache HBase. Zie het document [Start with Apache HBase in HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) voor meer informatie.|

## <a name="how-to-use-hive"></a>Hive gebruiken

Gebruik de volgende tabel om de verschillende manieren te ontdekken voor het gebruik van Hive met HDInsight:

| **Gebruik deze methode** als u wilt... | ... **interactieve** query's | ... **batch** verwerking | ... van dit **besturings systeem** van de client |
|:--- |:---:|:---:|:--- |:--- |
| [HDInsight-hulpprogram ma's voor Visual Studio code](../hdinsight-for-vscode.md) |✔ |✔ | Linux, UNIX, Mac OS X of Windows |
| [HDInsight-hulpprogram ma's voor Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Hive-weergave](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Any (op browser gebaseerd) |
| [Beeline-client](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, UNIX, Mac OS X of Windows |
| [REST API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, UNIX, Mac OS X of Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>Naslag informatie voor HiveQL language

HiveQL is een Naslag Gids voor talen beschikbaar in de [hand leiding voor talen](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive-en gegevens structuur

Hive begrijpt hoe u kunt werken met gestructureerde en semi-gestructureerde gegevens. Bijvoorbeeld tekst bestanden waarbij de velden worden gescheiden door specifieke tekens. De volgende HiveQL-instructie maakt een tabel op basis van door spaties gescheiden gegevens:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Hive ondersteunt ook aangepaste **serialisatiefunctie/deserializers (SerDe)** voor complexe of onregelmatige gestructureerde gegevens. Zie het [gebruik van een aangepaste JSON-SerDe met HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) -document voor meer informatie.

Voor meer informatie over bestands indelingen die worden ondersteund door Hive, raadpleegt u de [hand leiding voor talen ( https://cwiki.apache.org/confluence/display/Hive/LanguageManual) ](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Interne tabellen van Hive versus externe tabellen

Er zijn twee soorten tabellen die u met hive kunt maken:

* __Intern__: gegevens worden opgeslagen in het Hive-Data Warehouse. Het Data Warehouse bevindt zich `/hive/warehouse/` op de standaard opslag voor het cluster.

    Gebruik interne tabellen wanneer een van de volgende voor waarden van toepassing is:

    * De gegevens zijn tijdelijk.
    * U wilt dat de Hive de levens cyclus van de tabel en gegevens beheert.

* __Extern__: gegevens worden buiten het Data Warehouse opgeslagen. De gegevens kunnen worden opgeslagen op elke opslag die toegankelijk is voor het cluster.

    Gebruik externe tabellen wanneer een van de volgende voor waarden van toepassing is:

    * De gegevens worden ook buiten het Hive-onderdeel gebruikt. De gegevens bestanden worden bijvoorbeeld bijgewerkt door een ander proces (waardoor de bestanden niet worden vergrendeld.)
    * Gegevens moeten blijven op de onderliggende locatie, zelfs na het verwijderen van de tabel.
    * U hebt een aangepaste locatie nodig, zoals een niet-standaard opslag account.
    * Een ander programma dan Hive beheert de gegevens indeling, locatie, enzovoort.

Zie het blog bericht [Hive intern en externe tabellen Inleiding](/archive/blogs/cindygross/hdinsight-hive-internal-and-external-tables-intro) voor meer informatie.

## <a name="user-defined-functions-udf"></a>Door de gebruiker gedefinieerde functies (UDF)

Hive kan ook worden uitgebreid via door de **gebruiker gedefinieerde functies (UDF)**. Met een UDF kunt u functionaliteit of logica implementeren die niet eenvoudig kan worden gemodelleerd in HiveQL. Raadpleeg de volgende documenten voor een voor beeld van het gebruik van Udf's met hive:

* [Een door de gebruiker gedefinieerde Java-functie gebruiken met Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Een door de gebruiker gedefinieerde python-functie gebruiken met Apache Hive](../hadoop/python-udf-hdinsight.md)

* [Een door de gebruiker gedefinieerde C#-functie gebruiken met Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Een aangepaste Apache Hive door de gebruiker gedefinieerde functie toevoegen aan HDInsight](/archive/blogs/bigdatasupport/how-to-add-custom-hive-udfs-to-hdinsight)

* [Een voor beeld Apache Hive door de gebruiker gedefinieerde functie voor het converteren van datum/tijd-indelingen naar Hive-time stamp](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="example-data"></a>Voorbeeldgegevens

Hive op HDInsight wordt vooraf geladen met een interne tabel met de naam `hivesampletable` . HDInsight biedt ook voor beelden van gegevens sets die kunnen worden gebruikt met Hive. Deze gegevens sets worden opgeslagen in de `/example/data` `/HdiSamples` mappen en. Deze directory's bestaan in de standaard opslag voor uw cluster.

## <a name="example-hive-query"></a>Voor beeld Hive-query

De volgende HiveQL-instructies project kolommen naar het `/example/data/sample.log` bestand:

```hiveql
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
    GROUP BY t4;
```

In het vorige voor beeld voeren de HiveQL-instructies de volgende acties uit:

|Instructie |Beschrijving |
|---|---|
|DROP TABLE|Als de tabel al bestaat, verwijdert u deze.|
|CREATE EXTERNAL TABLE|Hiermee maakt u een nieuwe **externe** tabel in Hive. Externe tabellen slaan de tabel definitie in Hive alleen op. De gegevens blijven op de oorspronkelijke locatie en in de oorspronkelijke indeling.|
|RIJ-INDELING|Hiermee wordt aangegeven hoe de gegevens worden opgemaakt. In dit geval worden de velden in elk logboek gescheiden door een spatie.|
|OPGESLAGEN ALS TEXTFILE-LOCATIE|Hiermee wordt de component aangegeven waarin de gegevens worden opgeslagen (de `example/data` map) en opgeslagen als tekst. De gegevens kunnen zich in één bestand bevindt of zich verspreiden over meerdere bestanden in de map.|
|SELECT|Hiermee wordt het aantal rijen geselecteerd waarin de kolom **T4** de waarde **[error]** bevat. Deze instructie retourneert de waarde **3** omdat er drie rijen met deze waarde zijn.|
|INPUT__FILE__NAME zoals%. log|Hive probeert het schema toe te passen op alle bestanden in de map. In dit geval bevat de map bestanden die niet overeenkomen met het schema. Om te voor komen dat gegevens in de resultaten permanent worden opgehaald, wordt met deze verklaring aangegeven dat er alleen gegevens moeten worden geretourneerd van bestanden die eindigen op. log.|

> [!NOTE]  
> Externe tabellen moeten worden gebruikt wanneer u verwacht dat de onderliggende gegevens worden bijgewerkt door een externe bron. Bijvoorbeeld een geautomatiseerd proces voor het uploaden van gegevens of een MapReduce-bewerking.
>
> Als u een externe tabel verwijdert, worden de gegevens **niet** verwijderd, maar wordt alleen de tabel definitie verwijderd.

Als u een **interne** tabel in plaats van extern wilt maken, gebruikt u de volgende HiveQL:

```hiveql
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Met deze instructies worden de volgende acties uitgevoerd:

|Instructie |Beschrijving |
|---|---|
|CREATE TABLE ALS DEZE NIET BESTAAT|Als de tabel niet bestaat, maakt u deze. Omdat het **externe** sleutel woord niet wordt gebruikt, maakt deze instructie een interne tabel. De tabel wordt opgeslagen in het Hive-Data Warehouse en wordt volledig beheerd door Hive.|
|OPGESLAGEN ALS ORC|Hiermee worden de gegevens opgeslagen in de ORC-indeling (Optimized Row in kolommen). ORC is een zeer geoptimaliseerde en efficiënte indeling voor het opslaan van Hive-gegevens.|
|OVERSCHRIJVEN INVOEGEN... UITGESCHAKELD|Hiermee worden rijen uit de **log4jLogs** -tabel met **[error]** geselecteerd en worden de gegevens vervolgens ingevoegd in de tabel **errorLogs** .|

> [!NOTE]  
> In tegens telling tot externe tabellen verwijdert het verwijderen van een interne tabel ook de onderliggende gegevens.

## <a name="improve-hive-query-performance"></a>Hive-queryprestaties verbeteren

### <a name="apache-tez"></a>Apache TEZ

[Apache TEZ](https://tez.apache.org) is een raam werk waarmee gegevens intensieve toepassingen, zoals Hive, veel efficiënter op schaal kunnen worden uitgevoerd. TEZ is standaard ingeschakeld.  De [Apache Hive in TEZ-ontwerp documenten](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) bevat details over de implementatie keuzes en afstemmings configuraties.

### <a name="low-latency-analytical-processing-llap"></a>Analytische verwerking met lage latentie (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (ook wel bekend als Live Long en process) is een nieuwe functie in hive 2,0 waarmee query's in het geheugen kunnen worden opgeslagen in de cache. LLAP maakt Hive-query's veel sneller, tot [26x sneller dan Hive 1. x in sommige gevallen](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight biedt LLAP in het interactieve query cluster type. Zie het document [beginnen met interactieve query](../interactive-query/apache-interactive-query-get-started.md) voor meer informatie.

## <a name="scheduling-hive-queries"></a>Hive-query's plannen

Er zijn verschillende services die kunnen worden gebruikt om Hive-query's uit te voeren als onderdeel van een geplande of on-demand werk stroom.

### <a name="azure-data-factory"></a>Azure Data Factory

Met Azure Data Factory kunt u HDInsight gebruiken als onderdeel van een Data Factory-pijp lijn. Zie voor meer informatie over het gebruik van Hive vanuit een pijp lijn de [gegevens transformeren met hive-activiteit in azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md) document.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Hive-taken en-SQL Server Integration Services

U kunt SQL Server Integration Services (SSIS) gebruiken om een Hive-taak uit te voeren. Het Azure Feature Pack voor SSIS bevat de volgende onderdelen die werken met hive-taken in HDInsight.

* [Azure HDInsight-Hive-taak](/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Verbindings beheer voor Azure-abonnementen](/sql/integration-services/connection-manager/azure-subscription-connection-manager)

Zie de documentatie van het [Azure Feature Pack](/sql/integration-services/azure-feature-pack-for-integration-services-ssis) voor meer informatie.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie is een werk stroom-en coördinatie systeem waarmee Hadoop-taken worden beheerd. Zie voor meer informatie over het gebruik van Oozie with Hive de [Apache-Oozie gebruiken om een werk stroom document te definiëren en uit te voeren](../hdinsight-use-oozie-linux-mac.md) .

## <a name="next-steps"></a>Volgende stappen

Nu u weet welke Hive is en hoe u deze kunt gebruiken met Hadoop in HDInsight, gebruikt u de volgende koppelingen om andere manieren te verkennen om met Azure HDInsight te werken.

* [Gegevens uploaden naar HDInsight](../hdinsight-upload-data.md)
* [Met python door de gebruiker gedefinieerde functies (UDF) met Apache Hive en Apache varken in HDInsight gebruiken](./python-udf-hdinsight.md)
* [MapReduce-taken gebruiken met HDInsight](hdinsight-use-mapreduce.md)