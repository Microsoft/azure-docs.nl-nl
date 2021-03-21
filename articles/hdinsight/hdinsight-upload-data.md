---
title: Gegevens uploaden voor Apache Hadoop-taken in HDInsight
description: Meer informatie over het uploaden en openen van gegevens voor Apache Hadoop-taken in HDInsight. Gebruik klassieke CLI van Azure, Azure Storage Explorer, Azure PowerShell, de Hadoop-opdracht regel of Sqoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: a8d21b88298b6092c869b89d48fe4c259c2365b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931335"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Gegevens uploaden voor Apache Hadoop-taken in HDInsight

HDInsight biedt een Hadoop Distributed File System (HDFS) over Azure Storage en Azure Data Lake Storage. Deze opslag bevat gen1 en Gen2. Azure Storage en Data Lake Storage Gen1 en Gen2 zijn ontworpen als HDFS-extensies. Ze maken het mogelijk om de volledige set onderdelen in de Hadoop-omgeving rechtstreeks te laten werken met de gegevens die ze beheert. Azure Storage, Data Lake Storage Gen1 en Gen2 zijn afzonderlijke bestands systemen. De systemen zijn geoptimaliseerd voor opslag van gegevens en berekeningen voor die gegevens. Zie [Azure Storage gebruiken met HDInsight](hdinsight-hadoop-use-blob-storage.md)voor meer informatie over de voor delen van het gebruik van Azure Storage. Zie ook [Data Lake Storage gen1 met Hdinsight gebruiken](hdinsight-hadoop-use-data-lake-storage-gen1.md)en [Data Lake Storage Gen2 gebruiken met hdinsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Vereisten

Houd rekening met de volgende vereisten voordat u begint:

* Een Azure HDInsight-cluster. Zie [aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)voor instructies.
* Kennis van de volgende artikelen:
    * [Azure Storage gebruiken met HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Data Lake Storage Gen1 gebruiken met HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Data Lake Storage Gen2 gebruiken met HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Gegevens uploaden naar Azure Storage

### <a name="utilities"></a>Hulpprogramma's

Micro soft biedt de volgende hulpprogram ma's voor het werken met Azure Storage:

| Hulpprogramma | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure-CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop-opdracht](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> De Hadoop-opdracht is alleen beschikbaar op het HDInsight-cluster. Met deze opdracht kunt u alleen gegevens laden van het lokale bestands systeem naar Azure Storage.  

### <a name="hadoop-command-line"></a>Hadoop-opdracht regel

De Hadoop-opdracht regel kan alleen worden gebruikt voor het opslaan van gegevens in een Azure Storage-BLOB wanneer de gegevens al aanwezig zijn op het hoofd knooppunt van het cluster.

Als u de Hadoop-opdracht wilt gebruiken, moet u eerst verbinding maken met de hoofd knooppunt met behulp van [SSH of putty](hdinsight-hadoop-linux-use-ssh-unix.md).

Als de verbinding tot stand is gebracht, kunt u de volgende syntaxis gebruiken om een bestand te uploaden naar de opslag.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Bijvoorbeeld: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Omdat het standaard bestandssysteem voor HDInsight zich in Azure Storage bevindt, is/example/data/data.txt daad werkelijk in Azure Storage. U kunt ook naar het bestand verwijzen als:

`wasbs:///example/data/data.txt`

of

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

Zie voor een lijst met andere Hadoop-opdrachten die met bestanden werken [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> In Apache HBase-clusters is de standaard blok grootte die wordt gebruikt voor het schrijven van gegevens 256 KB. Dit werkt prima wanneer u HBase-Api's of REST-Api's gebruikt, met behulp `hadoop` van de of- `hdfs dfs` opdrachten voor het schrijven van gegevens die groter zijn dan ~ 12 GB. Dit resulteert in een fout. Zie voor meer informatie [opslag uitzondering voor schrijven op BLOB](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob).

### <a name="graphical-clients"></a>Grafische clients

Er zijn ook verschillende toepassingen die een grafische interface bieden voor het werken met Azure Storage. De volgende tabel bevat een lijst met enkele van deze toepassingen:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Micro soft Visual Studio Tools voor HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure-opslagverkenner](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer voor Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Azure Storage koppelen als lokale station

Zie [Azure Storage koppelen als lokale schijf](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive).

## <a name="upload-using-services"></a>Uploaden met Services

### <a name="azure-data-factory"></a>Azure Data Factory

De Azure Data Factory-service is een volledig beheerde service voor het opstellen van gegevens: opslag-, verwerkings-en verplaatsings Services in gestroomlijnde, flexibele en betrouw bare gegevens productie pijplijnen.

|Opslagtype|Documentatie|
|----|----|
|Azure Blob Storage|[Gegevens kopiëren naar of uit Azure Blob Storage met behulp van Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Gegevens kopiëren van of naar Azure Data Lake Storage Gen1 met behulp van Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Gegevens laden in Azure Data Lake Storage Gen2 met Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop is een hulp programma dat is ontworpen om gegevens over te dragen tussen Hadoop en relationele data bases. Gebruik het om gegevens te importeren uit een relationele Database Management System (RDBMS), zoals SQL Server, MySQL of Oracle. Vervolgens naar het Hadoop Distributed File System (HDFS). Transformeer de gegevens in Hadoop met MapReduce of Hive en exporteer de gegevens vervolgens terug naar een RDBMS.

Zie [Sqoop gebruiken met HDInsight](hadoop/hdinsight-use-sqoop.md)voor meer informatie.

### <a name="development-sdks"></a>Ontwikkel aars van Sdk's

Azure Storage kan ook worden geopend met behulp van een Azure SDK vanuit de volgende programmeer talen:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Voor meer informatie over het installeren van de Azure-Sdk's raadpleegt u [Azure down loads](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u gegevens in HDInsight kunt ophalen, lees dan de volgende artikelen voor meer informatie over analyse:

* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop-taken via een programma indienen](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)