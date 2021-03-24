---
title: Wat zijn Apache Hadoop en MapReduce - Azure HDInsight
description: Een inleiding tot HDInsight en de technologiestack en onderdelen van Apache Hadoop.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: ad1f7422919f224889db84a2599ad3c1c48efcc5
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863807"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Wat is Apache Hadoop in Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) was het originele open-source framework voor gedistribueerde verwerking en analyse van sets met big data in clusters. Het Hadoop-ecosysteem omvat verwante software en hulpprogramma's, waaronder Apache Hive, Apache HBase, Spark en Kafka.

Azure HDInsight is een volledig beheerde, zeer uitgebreide opensource-analyseservice in de cloud voor bedrijven. Met het Apache Hadoop-clustertype in Azure HDInsight kunt u het [Apache HDFS (Hadoop Distributed File System)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)-resourcebeheer en een eenvoudig [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)-programmeermodel gebruiken om batchgegevens parallel te verwerken en te analyseren.  Hadoop-clusters in HDInsight zijn compatibel met [Azure Blob Storage](../../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) of [Azure data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

Raadpleeg [Onderdelen en versies die beschikbaar zijn in HDInsight](../hdinsight-component-versioning.md) om na te gaan welke onderdelen van de Hadoop-technologiestack in HDInsight beschikbaar zijn. Zie de [pagina met Azure-functies voor HDInsight](https://azure.microsoft.com/services/hdinsight/) voor meer informatie over Hadoop in HDInsight.

## <a name="what-is-mapreduce"></a>Wat is MapReduce

[Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) is een softwareframework om taken te schrijven waarmee enorme hoeveelheden gegevens worden verwerkt. Invoergegevens worden in losstaande segmenten opgedeeld. Elk segment wordt parallel verwerkt via de knooppunten in uw cluster. Een MapReduce-taak bestaat uit twee functies:

* **Mapper**: Gebruikt invoergegevens, analyseert deze (meestal met filter- en sorteerbewerkingen) en verstuurt tuples (sleutel-waardeparen)

* **Reducer**: Gebruikt Tuples verzonden door de Mapper en voert een samenvattingsbewerking uit die een kleiner, gecombineerd resultaat maakt van de Mapper-gegevens

In het volgende diagram ziet u een voorbeeld een eenvoudige MapReduce-taak die woorden telt:

 :::image type="content" source="./media/apache-hadoop-introduction/hdi-word-count-diagram.gif" alt-text="HDI.WordCountDiagram" border="true":::

De uitvoer van deze taak is het aantal keer dat elk woord voorkomt in de tekst.

* De mapper neemt elke regel van de invoertekst als invoer en splitst die op in woorden. Het verzendt een sleutel/waarde-paar telkens wanneer een woord voorkomt en het woord wordt gevolgd door een 1. De uitvoer wordt gesorteerd voor deze naar de reducer wordt verzonden.
* De reducer somt deze individuele aantallen voor elk woord op en verzendt één sleutel/waarde-paar dat het woord en het aantal keer dat het voorkomt bevat.

MapReduce kan in verschillende talen worden geïmplementeerd. Java is de meest voorkomende implementatie en wordt in dit document gebruikt ter demonstratie.

## <a name="development-languages"></a>Ontwikkelingstalen

Talen of frameworks die gebaseerd zijn op Java en de Java Virtual Machine, kunnen rechtstreeks als een [MapReduce-taak](..//hadoop/submit-apache-hadoop-jobs-programmatically.md) worden uitgevoerd. Het voorbeeld dat in dit document gebruikt wordt, is een Java MapReduce-toepassing. Niet-Java-talen, zoals C#, Python of zelfstandige uitvoerbare bestanden, moeten **Hadoop-streaming** gebruiken.

Hadoop-streaming communiceert met de Mapper en de reducer via STDIN en STDOUT. De mapper en reducer lezen één regel gegevens per keer vanaf STDIN, en schrijven de uitvoer naar STDOUT. Elke regel de gelezen of geschreven wordt door de mapper en reducer moet de indeling van een sleutel/waarde-paar hebben, gescheiden door een tabteken:

`[key]\t[value]`

Zie [Hadoop-streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html) voor meer informatie.

Raadpleeg het volgende document voor voorbeelden van het gebruik van Hadoop-streaming met HDInsight:

* [C# MapReduce-taken ontwikkelen](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="where-do-i-start"></a>Waar moet ik beginnen?

* [Snelstart: Een Apache Hadoop-cluster maken in Azure HDInsight met Azure Portal](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Zelfstudie: Apache Hadoop-taken opgeven in HDInsight](../hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Java MapReduce-programma's ontwikkelen voor Apache Hadoop in HDInsight](../hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Apache Hive gebruiken als ETL-hulpprogramma (uitpakken, transformeren, laden)](../hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Uitpakken, transformeren en laden (ETL) op de juiste schaal](../hadoop/apache-hadoop-etl-at-scale.md)
* [Een pijplijn voor gegevensanalyse operationeel maken](../hdinsight-operationalize-data-pipeline.md)

## <a name="next-steps"></a>Volgende stappen

* [Apache Hadoop-cluster maken in HDInsight met behulp van de portal](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Een Apache Hadoop-cluster maken in HDInsight met een ARM-sjabloon](../hadoop/apache-hadoop-linux-tutorial-get-started.md)