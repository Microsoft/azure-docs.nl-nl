---
title: Wat is Apache Spark - Azure HDInsight
description: In dit artikel maakt u kennis met Spark in HDInsight en de verschillende scenario's waarin u een Spark-cluster kunt gebruiken in HDInsight.
ms.service: hdinsight
ms.custom: contperf-fy21q1
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 9f5121feebbb516e148b0476d6c8280d461237bc
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929979"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Wat is Apache Spark in Azure HDInsight?

Apache Spark is een framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. Apache Spark in Azure HDInsight is Microsoft's implementatie van Apache Spark in de cloud. Met HDInsight kunt u eenvoudiger Spark-clusters maken en configureren in Azure. Spark-clusters in HDInsight zijn compatibel met [Azure Blob Storage](../../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) of [Azure data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md). Dit betekent dat u HDInsight Spark-clusters kunt gebruiken om gegevens te verwerken die zijn opgeslagen in Azure. Zie [Apache Hadoop-onderdelen en versies in Azure HDInsight](../hdinsight-component-versioning.md) voor informatie over de onderdelen en versies.

![Spark: een geünificeerd framework](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Wat is Apache Spark?

Spark biedt primitieve typen voor in-memory clustercomputing. Een Spark-taak kan gegevens laden en in het geheugen cachen en er herhaaldelijk query’s op uitvoeren. Verwerkingen in het geheugen (in-memory) zijn veel sneller dan toepassingen die op schijf worden uitgevoerd, zoals Hadoop, dat gegevens deelt via HDFS (Hadoop Distributed File System). Spark kan ook worden geïntegreerd met de programmeertaal Scala waarmee u gedistribueerde gegevenssets zoals lokale verzamelingen kunt bewerken. Het is niet nodig om alles te structureren als toewijzings- en verminderingsbewerkingen.

![Traditionele MapReduce vergeleken met Spark](./media/apache-spark-overview/map-reduce-vs-spark1.png)

Spark-clusters in HDInsight bieden een volledig beheerde Spark-service. Dit zijn de voordelen van het maken van een Spark-cluster in HDInsight.

| Functie | Beschrijving |
| --- | --- |
| Eenvoudig te maken |U kunt binnen enkele minuten een nieuw Spark-cluster in HDInsight maken met Azure Portal, Azure PowerShell of de .NET-SDK voor HDInsight. Zie [Aan de slag met Apache Spark-clusters in HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Gebruiksgemak |Spark-cluster in HDInsight bevat Jupyter-notebooks en Apache Zeppelin-notebooks. U kunt deze notebooks gebruiken voor interactieve gegevensverwerking en visualisatie. Zie [Apache Zeppelin-notebooks gebruiken met Apache Spark](apache-spark-zeppelin-notebook.md) en [Gegevens laden en query's uitvoeren op een Apache Spark-cluster](apache-spark-load-data-run-query.md).|
| REST-API’s |Apache Spark-clusters in HDInsight omvatten [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), een op REST API gebaseerde Apache Spark-taakserver om op afstand taken te verzenden en te controleren. Zie [Apache Spark REST API gebruiken voor het verzenden van externe taken naar een HDInsight Spark-cluster](apache-spark-livy-rest-interface.md).|
| Ondersteuning voor Azure Storage | Spark-clusters in HDInsight kunnen Azure Data Lake Storage Gen1/Gen2 gebruiken als de primaire opslag of als extra opslag. Zie [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) voor meer informatie over Data Lake Storage Gen1. Zie [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) voor meer informatie over Data Lake Storage Gen2.|
| Integratie met Azure-services |Spark-clusters in HDInsight worden geleverd met een connector voor Azure Event Hubs. U kunt met de Event Hubs streamingtoepassingen bouwen. Dit is inclusief Apache Kafka, die al beschikbaar is als onderdeel van Spark. |
| Ondersteuning voor ML Server | Ondersteuning voor ML Server in HDInsight wordt geleverd als het clustertype **ML Services**. U kunt een ML Services-cluster instellen om gedistribueerde R-berekeningen uit te voeren met de snelheden van een Spark-cluster. Zie [Wat is ML Services in Azure HDInsight?](../r-server/r-server-overview.md) voor meer informatie. |
| Integratie met IDE's van derden | HDInsight biedt verschillende IDE-invoegtoepassingen die handig zijn voor het maken en verzenden van toepassingen naar een HDInsight Spark-cluster. Raadpleeg [Azure-toolkit voor IntelliJ IDEA gebruiken](apache-spark-intellij-tool-plugin.md), [Spark en Hive Tools voor VSCode gebruiken](../hdinsight-for-vscode.md) en [Azure-toolkit voor Eclipse gebruiken](apache-spark-eclipse-tool-plugin.md) voor meer informatie.|
| Gelijktijdige query's |Spark-clusters in HDInsight ondersteunen gelijktijdige query's. Hierdoor kunnen meerdere query's van één gebruiker of meerdere query's van verschillende gebruikers en toepassingen dezelfde clusterresources delen. |
| Opslaan in cache in SSD's |U kunt gegevens in het geheugen of in aan de clusterknooppunten gekoppelde SSD's cachen. Cachen in het geheugen biedt de beste queryprestaties, maar kan duur zijn. Cachen op SSD's is een goed alternatief voor het verbeteren van de prestaties van query's zonder dat er een cluster hoeft te worden gemaakt dat zo groot is dat de volledige gegevensset in het geheugen past. Zie [Prestaties van Apache Spark-werkbelastingen verbeteren met behulp van IO Cache van Azure HDInsight](apache-spark-improve-performance-iocache.md). |
| Integratie met BI-tools |Spark-clusters in HDInsight bieden connectors voor BI-tools zoals Power BI voor gegevensanalyse. |
| Vooraf geladen Anaconda-bibliotheken |Spark-clusters in HDInsight worden geleverd met Anaconda-bibliotheken die vooraf zijn geïnstalleerd. [Anaconda](https://docs.continuum.io/anaconda/) voorziet in bijna 200 bibliotheken voor machine learning, gegevensanalyse, visualisatie, enzovoort. |
| Aanpasbaarheid | Met HDInsight kunt u het aantal clusterknooppunten dynamisch wijzigen met behulp van de functie Automatische schaalaanpassing. Zie [Azure HDInsight-clusters automatisch schalen](../hdinsight-autoscale-clusters.md). Bovendien kunnen Spark-clusters zonder gegevensverlies worden verwijderd, omdat alle gegevens worden opgeslagen in Azure Blob Storage, [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) of [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md). |
| SLA |Spark-clusters in HDInsight worden geleverd met 24/7 ondersteuning en een SLA met een beschikbaarheid van 99,9%. |

Apache Spark-clusters in HDInsight omvatten de volgende onderdelen die standaard beschikbaar zijn in de clusters.

* [Spark Core](https://spark.apache.org/docs/latest/). Omvat Spark Core, Spark SQL, Spark-streaming-API's, GraphX en MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Apache Zeppelin-notebook](http://zeppelin-project.org/)

HDInsight Spark clustert een [ODBC-stuurprogramma](https://go.microsoft.com/fwlink/?LinkId=616229) voor connectiviteit met BI-hulpprogramma's zoals Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Architectuur van Spark-cluster

![De architectuur van HDInsight Spark](./media/apache-spark-overview/hdi-spark-architecture.png)

De onderdelen van Spark zijn gemakkelijker te begrijpen als u weet hoe Spark wordt uitgevoerd in HDInsight-clusters.

Spark-toepassingen worden als onafhankelijke sets met processen op een cluster uitgevoerd. Dit wordt gecoördineerd door het SparkContext-object in uw hoofdprogramma (dit wordt het stuurprogramma genoemd).

De SparkContext kan verbinding maken met verschillende typen clustermanagers, die resources bieden voor toepassingen. Voorbeelden van clustermanagers zijn Apache Mesos, Apache Hadoop YARN en de clustermanager van Apache Spark. In HDInsight wordt Spark uitgevoerd met behulp van de YARN-clustermanager. Als er verbinding is, verkrijgt Spark executors op werkknooppunten in het cluster. Dit zijn processen die berekeningen uitvoeren en gegevens opslaan voor uw toepassing. Vervolgens wordt de code van uw toepassing (gedefinieerd in JAR- of Python-bestanden die worden doorgegeven aan SparkContext) verstuurd naar de executor. Ten slotte verzendt SparkContext taken naar de executors om uit te voeren.

SparkContext voert de hoofdfunctie van de gebruiker uit en voert de verschillende parallelle bewerkingen uit op de werkknooppunten. Vervolgens verzamelt SparkContext de resultaten van de bewerkingen. De werkrolknooppunten lezen en schrijven gegevens van en naar het HDFS (Hadoop Distributed File System). De werkrolknooppunten plaatsen getransformeerde gegevens ook in het geheugen als RDD’s (Resilient Distributed Datasets).

SparkContext maakt verbinding met de Spark-master en converteert een toepassing naar een Directed Acyclic Graph (DAG) van afzonderlijke taken die worden uitgevoerd in een uitvoerproces op de werkknooppunten. Elke toepassing krijgt zijn eigen executorprocessen, die voor de duur van de gehele toepassing blijven en taken uitvoeren in meerdere threads.

## <a name="spark-in-hdinsight-use-cases"></a>Gebruiksscenario's voor Spark in HDInsight

Spark-clusters in HDInsight maken de volgende belangrijke scenario's mogelijk:

### <a name="interactive-data-analysis-and-bi"></a>Interactieve gegevensanalyse en BI

Apache Spark in HDInsight slaat gegevens op in Azure Blob Storage, Azure Data Lake Gen1 of Azure Data Lake Storage Gen2. Zakelijke deskundigen en belangrijke besluitvormers kunnen met die gegevens rapporten analyseren en bouwen en Microsoft Power BI gebruiken om interactieve rapporten te bouwen op basis van de geanalyseerde gegevens. Analisten kunnen beginnen met ongestructureerde/semi-gestructureerde gegevens in clusteropslag, een schema voor de gegevens definiëren met behulp van notebooks, en vervolgens gegevensmodellen bouwen met behulp van Microsoft Power BI. Spark-clusters in HDInsight bieden tevens ondersteuning voor een aantal externe BI-hulpprogramma's, zoals Tableau, waarmee gegevensanalisten, zakelijke deskundigen en belangrijke besluitvormers gemakkelijker kunnen werken.

* [Zelfstudie: Spark-gegevens visualiseren met behulp van Power BI](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Machine Learning in Spark

Apache Spark wordt meegeleverd met [MLlib](https://spark.apache.org/mllib/). MLlib is een bibliotheek voor machine learning die boven op Spark is gebouwd. U kunt deze bibliotheek gebruiken vanuit een Spark-cluster in HDInsight. Spark-clusters in HDInsight bevatten ook Anaconda, een Python-distributie met verschillende soorten pakketten voor machine learning. Gecombineerd met ingebouwde ondersteuning voor Jupyter- en Zeppelin-notebooks beschikt u hiermee over een omgeving voor het maken van machine learning-toepassingen.

* [Zelfstudie: Gebouwtemperaturen voorspellen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)  
* [Zelfstudie: Resultaten van voedingsinspectie voorspellen](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Streaming en realtime gegevensanalyse in Spark

Spark-clusters in HDInsight bieden uitgebreide ondersteuning voor het bouwen van realtime analyseoplossingen. Spark heeft al connectors om gegevens uit vele bronnen zoals Kafka, Flume, Twitter, ZeroMQ of TCP-sockets op te nemen. Met Spark in HDInsight wordt hoogwaardige ondersteuning voor het op van gegevens uit Azure Event Hubs toegevoegd. Event Hubs is de meest gebruikte wachtrijservice in Azure. Dankzij volledige ondersteuning voor Event Hubs zijn Spark-clusters in HDInsight een ideaal platform voor het bouwen van een realtime analysepijplijn.

* [Overzicht van Apache Spark-streaming](apache-spark-streaming-overview.md)
* [Overzicht van Gestructureerde streaming van Apache Spark](apache-spark-structured-streaming-overview.md)

## <a name="next-steps"></a>Volgende stappen

In dit overzicht hebt u algemene informatie gekregen over het gebruik van Apache Spark in Azure HDInsight.  U kunt de volgende artikelen gebruiken om meer te weten te komen over Apache Spark in HDInsight, en u kunt een HDInsight Spark-cluster maken en een aantal voorbeelden van Spark-query's uitvoeren:

* [Snelstart: Een Apache Spark-cluster maken in HDInsight en een interactieve query uitvoeren met Jupyter](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Zelfstudie: gegevens laden en query's uitvoeren in een Apache Spark-taak met behulp van Jupyter](./apache-spark-load-data-run-query.md)
* [Zelfstudie: Spark-gegevens visualiseren met behulp van Power BI](apache-spark-use-bi-tools.md)
* [Zelfstudie: Gebouwtemperaturen voorspellen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Spark-taken voor prestaties optimaliseren](apache-spark-perf.md)