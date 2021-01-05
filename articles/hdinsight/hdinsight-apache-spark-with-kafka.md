---
title: Apache Spark streamen met Apache Kafka-Azure HDInsight
description: Meer informatie over het gebruik van Apache Spark voor het streamen van gegevens naar of van Apache Kafka met behulp van DStreams. In dit voor beeld streamt u gegevens met behulp van een Jupyter Notebook van Spark in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 88a62b2d1ebee3bf933729ab82bc9e37a3db066b
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821176"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark streaming (DStream)-voor beeld met Apache Kafka in HDInsight

Meer informatie over het gebruik van [Apache Spark](https://spark.apache.org/) voor het streamen van gegevens naar of van [Apache Kafka](https://kafka.apache.org/) op HDInsight met behulp van [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). In dit voor beeld wordt een [Jupyter notebook](https://jupyter.org/) gebruikt dat wordt uitgevoerd op het Spark-cluster.

> [!NOTE]  
> Met de stappen in dit document wordt een Azure-resourcegroep gemaakt die zowel een Spark in HDInsight- als een Kafka in HDInsight-cluster bevat. Deze clusters bevinden zich beide binnen een Azure Virtual Network, waardoor het Spark-cluster rechtstreeks kan communiceren met het Kafka-cluster.
>
> Nadat u de stappen in dit document hebt doorlopen, moet u niet vergeten de clusters te verwijderen om overtollige kosten te voorkomen.

> [!IMPORTANT]  
> In dit voor beeld wordt DStreams gebruikt. Dit is een oudere Spark-streaming-technologie. Zie voor een voor beeld waarin nieuwe functies van Spark-streaming worden gebruikt de [Spark Structured streaming met Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) document.

## <a name="create-the-clusters"></a>De clusters maken

Apache Kafka in HDInsight biedt geen toegang tot de Kafka-brokers via het openbare internet. Alles wat er naar Kafka praat, moet zich in hetzelfde virtuele Azure-netwerk bevindt als de knoop punten in het Kafka-cluster. In dit voor beeld bevinden zowel de Kafka-als Spark-clusters zich in een virtueel Azure-netwerk. In het volgende diagram ziet u hoe communicatie stromen tussen de clusters:

![Diagram van Spark- en Kafka-clusters in een Azure Virtual Network](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Hoewel Kafka zichzelf beperkt is tot communicatie binnen het virtuele netwerk, kunnen andere services op het cluster, zoals SSH en Ambari, via internet worden benaderd. Zie [Poorten en URI's die worden gebruikt door HDInsight](hdinsight-hadoop-port-settings-for-services.md) voor meer informatie over de openbare poorten die beschikbaar zijn voor HDInsight.

Hoewel u hand matig een Azure Virtual Network-, Kafka-en Spark-cluster kunt maken, is het eenvoudiger om een Azure Resource Manager sjabloon te gebruiken. Gebruik de volgende stappen om een Azure Virtual Network-, Kafka-en Spark-cluster te implementeren in uw Azure-abonnement.

1. Gebruik de volgende knop om u aan te melden bij Azure en de sjabloon in de Azure Portal te openen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    De Azure Resource Manager-sjabloon bevindt zich op **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

    > [!WARNING]  
    > Om beschikbaarheid van Kafka op HDInsight te garanderen, moet uw cluster ten minste drie werkknooppunten bevatten. Met deze sjabloon maakt u een Kafka-cluster dat drie werkknooppunten bevat.

    Met deze sjabloon maakt u een HDInsight 3,6-cluster voor zowel Kafka als Spark.

1. Gebruik de volgende informatie om de vermeldingen in de sectie **aangepaste implementatie** in te vullen:

    |Eigenschap |Waarde |
    |---|---|
    |Resourcegroep|Een groep maken of een bestaande selecteren.|
    |Locatie|Selecteer een locatie die geografisch dicht bij u ligt.|
    |Basis cluster naam|Deze waarde wordt gebruikt als de basis naam voor de Spark-en Kafka-clusters. Als u bijvoorbeeld **hdistreaming** invoert, wordt een Spark-cluster met de naam __Spark-Hdistreaming__ en een Kafka-cluster met de naam **Kafka-hdistreaming** gemaakt.|
    |Gebruikersnaam voor clusteraanmelding|De gebruikers naam van de beheerder voor de Spark-en Kafka-clusters.|
    |Wachtwoord voor clusteraanmelding|Het gebruikers wachtwoord van de beheerder voor de Spark-en Kafka-clusters.|
    |SSH-gebruikersnaam|De SSH-gebruiker die moet worden gemaakt voor de Spark-en Kafka-clusters.|
    |SSH-wachtwoord|Het wacht woord voor de SSH-gebruiker voor de Spark-en Kafka-clusters.|

    ![Aangepaste implementatie parameters voor HDInsight](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. Lees de **voorwaarden** en schakel vervolgens het selectievakje **Ik ga akkoord met de bovenstaande voorwaarden** in.

1. Selecteer ten slotte **Inkoop**. Het duurt ongeveer 20 minuten om de clusters te maken.

Zodra de resources zijn gemaakt, wordt een overzichts pagina weer gegeven.

![Overzicht van de resource groep voor de vnet en clusters](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> U ziet dat de namen van de HDInsight **-clusters Spark-basenaam** en **Kafka-base-** naam zijn, waarbij basenaam de door u aangelegde mapnaam is. U gebruikt deze namen in latere stappen wanneer u verbinding maakt met de clusters.

## <a name="use-the-notebooks"></a>De notitie blokken gebruiken

De code voor het voor beeld dat in dit document wordt beschreven, is beschikbaar op [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) .

## <a name="delete-the-cluster"></a>Het cluster verwijderen

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Omdat de stappen in dit document beide clusters maken in dezelfde Azure-resource groep, kunt u de resource groep verwijderen in de Azure Portal. Als u de groep verwijdert, worden alle resources verwijderd die zijn gemaakt met behulp van dit document, de Azure Virtual Network en het opslag account dat door de clusters wordt gebruikt.

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld hebt u geleerd hoe u Spark kunt gebruiken om te lezen en te schrijven naar Kafka. Gebruik de volgende koppelingen om andere manieren te ontdekken voor het werken met Kafka:

* [Aan de slag met Apache Kafka op HDInsight](kafka/apache-kafka-get-started.md)
* [MirrorMaker gebruiken voor het maken van een replica van Apache Kafka in HDInsight](kafka/apache-kafka-mirroring.md)
* [Apache Storm gebruiken met Apache Kafka op HDInsight](hdinsight-apache-storm-with-kafka.md)
