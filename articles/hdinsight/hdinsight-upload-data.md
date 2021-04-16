---
title: Gegevens uploaden voor Apache Hadoop-taken in HDInsight
description: Meer informatie over het uploaden en openen van gegevens voor Apache Hadoop-taken in HDInsight. Gebruik de klassieke Azure CLI, Azure Storage Explorer, Azure PowerShell, de Hadoop-opdrachtregel of Sqoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020, devx-track-azurecli
ms.date: 04/27/2020
ms.openlocfilehash: d58d194e8dbf011eec949602e4f8cd2e084a0d98
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482106"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Gegevens uploaden voor Apache Hadoop-taken in HDInsight

HDInsight biedt een Hadoop Distributed File System (HDFS) via Azure Storage en Azure Data Lake Storage. Deze opslag omvat Gen1 en Gen2. Azure Storage en Data Lake Storage Gen1 Gen2 zijn ontworpen als HDFS-extensies. Ze zorgen ervoor dat de volledige set onderdelen in de Hadoop-omgeving rechtstreeks kan worden gebruikt voor de gegevens die worden beheert. Azure Storage, Data Lake Storage Gen1 en Gen2 zijn afzonderlijke bestandssystemen. De systemen zijn geoptimaliseerd voor de opslag van gegevens en berekeningen op die gegevens. Zie Use Azure Storage with HDInsight (Een Azure Storage gebruiken met HDInsight) voor meer informatie over de voordelen [van het gebruik van hdinsight.](hdinsight-hadoop-use-blob-storage.md) Zie ook [Data Lake Storage Gen1 gebruiken met HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)en Data Lake Storage Gen2 gebruiken met [HDInsight.](hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="prerequisites"></a>Vereisten

Houd rekening met de volgende vereisten voordat u begint:

* Een Azure HDInsight-cluster. Zie Aan de slag met Azure HDInsight voor [instructies.](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* Kennis van de volgende artikelen:
    * [Een Azure Storage HDInsight gebruiken](hdinsight-hadoop-use-blob-storage.md)
    * [Een Data Lake Storage Gen1 HDInsight gebruiken](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Een Data Lake Storage Gen2 HDInsight gebruiken](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Gegevens uploaden naar Azure Storage

### <a name="utilities"></a>Hulpprogramma's

Microsoft biedt de volgende hulpprogramma's voor het werken met Azure Storage:

| Hulpprogramma | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-portal](../storage/blobs/storage-quickstart-blobs-portal.md) |ââ" |ââ" |ââ" |
| [Azure-CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |ââ" |ââ" |ââ" |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |ââ" |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |ââ" | |ââ" |
| [Hadoop-opdracht](#hadoop-command-line) |ââ" |ââ" |ââ" |

> [!NOTE]  
> De Hadoop-opdracht is alleen beschikbaar in het HDInsight-cluster. Met de opdracht kunt u alleen gegevens uit het lokale bestandssysteem laden in Azure Storage.  

### <a name="hadoop-command-line"></a>Hadoop-opdrachtregel

De Hadoop-opdrachtregel is alleen nuttig voor het opslaan van gegevens in een Azure-opslagblob wanneer de gegevens al aanwezig zijn op het hoofdknooppunt van het cluster.

Als u de Hadoop-opdracht wilt gebruiken, moet u eerst verbinding maken met het hoofdnode met [behulp van SSH of PuTTY.](hdinsight-hadoop-linux-use-ssh-unix.md)

Zodra er verbinding is gemaakt, kunt u de volgende syntaxis gebruiken om een bestand naar de opslag te uploaden.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Bijvoorbeeld: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Omdat het standaardbestandssysteem voor HDInsight zich in Azure Storage, is /example/data/data.txt in feite in Azure Storage. U kunt ook naar het bestand verwijzen als:

`wasbs:///example/data/data.txt`

of

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

Zie voor een lijst met andere Hadoop-opdrachten die met bestanden werken [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> In Apache HBase-clusters is de standaardblokgrootte die wordt gebruikt bij het schrijven van gegevens 256 kB. Hoewel dit prima werkt bij het gebruik van HBase API's of REST API's, resulteert het gebruik van de opdrachten of om gegevens te schrijven die groter zijn dan `hadoop` `hdfs dfs` ~12 GB in een fout. Zie Opslag-uitzondering voor schrijven [op blob voor meer informatie.](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob)

### <a name="graphical-clients"></a>Grafische clients

Er zijn ook verschillende toepassingen die een grafische interface bieden voor het werken met Azure Storage. De volgende tabel is een lijst met enkele van deze toepassingen:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools voor HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |ââ" |ââ" |ââ" |
| [Azure-opslagverkenner](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |ââ" |ââ" |ââ" |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |ââ" |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |ââ" |
| [CloudBerry Explorer voor Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |ââ" |
| [Cyberduck](https://cyberduck.io/) | |ââ" |ââ" |

## <a name="mount-azure-storage-as-local-drive"></a>Een Azure Storage als lokaal station

Zie [Azure Storage als Lokaal station.](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive)

## <a name="upload-using-services"></a>Uploaden met behulp van services

### <a name="azure-data-factory"></a>Azure Data Factory

De Azure Data Factory service is een volledig beheerde service voor het samenstellen van gegevens: opslag, verwerking en verplaatsing van services in gestroomlijnde, aanpasbare en betrouwbare pijplijnen voor gegevensproductie.

|Opslagtype|Documentatie|
|----|----|
|Azure Blob Storage|[Gegevens kopiëren naar of uit Azure Blob Storage met behulp van Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Gegevens kopiëren naar of van Azure Data Lake Storage Gen1 met behulp van Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Gegevens laden in Azure Data Lake Storage Gen2 met Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop is een hulpprogramma dat is ontworpen om gegevens over te dragen tussen Hadoop en relationele databases. Gebruik het om gegevens te importeren uit een relationeel databasebeheersysteem (RDBMS), zoals SQL Server, MySQL of Oracle. Ga vervolgens naar het Hadoop Distributed File System (HDFS). Transformeer de gegevens in Hadoop met MapReduce of Hive en exporteer de gegevens vervolgens weer naar een RDBMS.

Zie [Sqoop gebruiken met HDInsight voor meer informatie.](hadoop/hdinsight-use-sqoop.md)

### <a name="development-sdks"></a>Ontwikkelings-SDK's

Azure Storage zijn ook toegankelijk met behulp van een Azure SDK vanuit de volgende programmeertalen:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Zie Azure-downloads voor meer informatie over het installeren van [de Azure-SDK's](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u gegevens in HDInsight kunt krijgen, kunt u de volgende artikelen lezen voor meer informatie over analyse:

* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop-taken programmatisch verzenden](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
