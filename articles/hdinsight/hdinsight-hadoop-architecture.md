---
title: 'Apache Hadoop architectuur: Azure HDInsight'
description: Beschrijft Apache Hadoop opslag en verwerking op Azure HDInsight-clusters.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/07/2020
ms.openlocfilehash: f69917b574c0be8fdacf05934b05544a4f491a9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864190"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Apache Hadoop-architectuur in HDInsight

[Apache Hadoop](https://hadoop.apache.org/) bevat twee kern onderdelen: de [Apache HADOOP Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) die opslag biedt en [Apache Hadoop nog een andere resource-onderhandel (garens)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) die verwerkings mogelijkheden bieden. Met de opslag-en verwerkings functionaliteit wordt een cluster in staat om [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) -Program ma's uit te voeren om de gewenste gegevens verwerking uit te voeren.

> [!NOTE]  
> Een HDFS wordt doorgaans niet geïmplementeerd in het HDInsight-cluster om opslag te bieden. In plaats daarvan wordt een met HDFS compatibele interface laag gebruikt door Hadoop-onderdelen. De werkelijke opslag capaciteit wordt gegeven door Azure Storage of Azure Data Lake Storage. Voor Hadoop worden MapReduce-taken die worden uitgevoerd op het HDInsight-cluster uitgevoerd alsof er een HDFS aanwezig was, waardoor er geen wijzigingen nodig zijn om de opslag behoeften te ondersteunen. In Hadoop op HDInsight is opslag uitbesteed, maar garen verwerking blijft een kern onderdeel. Zie [Introduction to Azure HDInsight](hadoop/apache-hadoop-introduction.md)(Engelstalig) voor meer informatie.

In dit artikel worden GARENs geïntroduceerd en hoe het de uitvoering van toepassingen op HDInsight coördineert.

## <a name="apache-hadoop-yarn-basics"></a>Basis beginselen van Apache Hadoop garen

GARENs regelen en organiseren gegevens verwerking in Hadoop. GARENs hebben twee kern services die als processen worden uitgevoerd op knoop punten in het cluster:

* ResourceManager
* NodeManager

De Resource Manager verleent cluster Compute-resources aan toepassingen als MapReduce-taken. De Resource Manager geeft deze bronnen als containers, waarbij elke container bestaat uit een toewijzing van CPU-kernen en RAM-geheugen. Als u alle beschik bare resources in een cluster hebt gecombineerd en vervolgens de kernen en het geheugen in blokken hebt gedistribueerd, is elk bronnen blok een container. Elk knoop punt in het cluster heeft een capaciteit voor een bepaald aantal containers. Daarom heeft het cluster een vaste limiet voor het aantal beschik bare containers. De service-eenheid van resources in een container kan worden geconfigureerd.

Wanneer een MapReduce-toepassing wordt uitgevoerd op een cluster, biedt de Resource Manager de toepassing de containers waarin moet worden uitgevoerd. De resource manager houdt de status van het uitvoeren van toepassingen, de beschik bare cluster capaciteit en houdt bij het bijhouden van de toepassingen op de markt.

De Resource Manager voert ook een webserver proces uit dat een webgebruikersinterface biedt om de status van toepassingen te controleren.

Wanneer een gebruiker een MapReduce-toepassing indient om uit te voeren op het cluster, wordt de toepassing verzonden naar de Resource Manager. De resource manager wijst op zijn beurt een container toe op beschik bare NodeManager-knoop punten. De NodeManager-knoop punten zijn waar de toepassing daad werkelijk wordt uitgevoerd. De eerste toegewezen container voert een speciale toepassing uit met de naam ApplicationMaster. Deze ApplicationMaster is verantwoordelijk voor het verkrijgen van resources, in de vorm van volgende containers, die nodig zijn om de ingediende toepassing uit te voeren. De ApplicationMaster onderzoekt de fasen van de toepassing, zoals de kaart fase en de fase verminderen, en factoren in hoeveel gegevens er moeten worden verwerkt. De ApplicationMaster vervolgens aanvragen (*onderhandelt*) de bronnen uit de resource manager namens de toepassing. De Resource Manager geeft resources van de NodeManagers in het cluster door aan de ApplicationMaster om deze te gebruiken voor het uitvoeren van de toepassing.

De NodeManagers voert de taken uit waaruit de toepassing is opgebouwd en rapporteert de voortgang en de status terug naar de ApplicationMaster. Met de ApplicationMaster in wordt de status van de toepassing weer gegeven in de Resource Manager. De Resource Manager retourneert alle resultaten naar de client.

## <a name="yarn-on-hdinsight"></a>GARENs op HDInsight

Alle typen HDInsight-clusters implementeren GARENs. De Resource Manager wordt geïmplementeerd voor hoge Beschik baarheid met een primair en secundair exemplaar, dat respectievelijk wordt uitgevoerd op de eerste en tweede hoofd knooppunten in het cluster. Er is slechts één exemplaar van de Resource Manager tegelijk actief. De NodeManager-instanties worden uitgevoerd op de beschik bare worker-knoop punten in het cluster.

:::image type="content" source="./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png" alt-text="Apache-GARENs op Azure HDInsight" border="false":::

## <a name="soft-delete"></a>Voorlopig verwijderen

Als u het verwijderen van een bestand uit uw opslag account ongedaan wilt maken, raadpleegt u:

### <a name="azure-storage"></a>Azure Storage

* [Voorlopig verwijderen voor Azure Storage-blobs](../storage/blobs/soft-delete-blob-overview.md)
* [BLOB verwijderen](/rest/api/storageservices/undelete-blob)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

[Restore-AzDataLakeStoreDeletedItem](/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem)

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

[Bekende problemen met Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-known-issues.md)

## <a name="trash-purging"></a>Prullenbak verwijderen

De `fs.trash.interval` eigenschap van de  >  **geavanceerde kern-site** van HDFS moet op de standaard waarde blijven `0` omdat u geen gegevens op het lokale bestands systeem hoeft op te slaan. Deze waarde heeft geen invloed op externe opslag accounts (WASB, ADLS GEN1, ABFS)

## <a name="next-steps"></a>Volgende stappen

* [MapReduce gebruiken in Apache Hadoop in HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Inleiding tot Azure HDInsight](hadoop/apache-hadoop-introduction.md)