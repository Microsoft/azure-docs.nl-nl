---
title: Azure Storage-oplossingen voor ML-Services op HDInsight-Azure
description: Meer informatie over de verschillende opslag opties die beschikbaar zijn met ML Services in HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/02/2020
ms.openlocfilehash: ddc48025de164ff68fb539a293e06bae09171742
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943912"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure Storage-oplossingen voor ML-Services in azure HDInsight

In een service op basis van ML kunnen verschillende opslag oplossingen worden gebruikt voor het persistent maken van gegevens, code of objecten die resultaten uit de analyse bevatten. Deze oplossingen omvatten de volgende opties:

- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage Gen1](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure File Storage](https://azure.microsoft.com/services/storage/files/)

U hebt ook de mogelijkheid om toegang te krijgen tot meerdere Azure Storage-accounts of-containers met uw HDInsight-cluster. Azure File Storage is een handige optie voor gegevens opslag voor gebruik op het Edge-knoop punt waarmee u een Azure Storage-bestands share kunt koppelen aan bijvoorbeeld het Linux-bestands systeem. Azure-bestands shares kunnen worden gekoppeld en gebruikt door elk systeem met een ondersteund besturings systeem, zoals Windows of Linux.

Wanneer u een Apache Hadoop cluster maakt in HDInsight, geeft u een **Azure Blob-opslag** account of **Data Lake Storage gen1** op. Een specifieke opslag container van dat account bevat het bestands systeem voor het cluster dat u maakt (bijvoorbeeld de Hadoop Distributed File System). Zie voor meer informatie en richt lijnen:

- [Azure Blob-opslag gebruiken met HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Data Lake Storage Gen1 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Azure Blob Storage-accounts gebruiken met een cluster van ML Services

Als u meer dan één opslag account hebt opgegeven tijdens het maken van uw ML-cluster, wordt in de volgende instructies uitgelegd hoe u een secundair account gebruikt voor gegevens toegang en-bewerkingen op een ML-cluster. Stel dat de volgende opslag accounts en container: **storage1** en een standaard container met de naam **container1** en **storage2** met **container2**.

> [!WARNING]  
> Voor prestatie doeleinden wordt het HDInsight-cluster in hetzelfde Data Center gemaakt als het primaire opslag account dat u opgeeft. Het gebruik van een opslag account op een andere locatie dan het HDInsight-cluster wordt niet ondersteund.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>De standaard opslag gebruiken met ML-Services in HDInsight

1. Gebruik een SSH-client om verbinding te maken met het Edge-knoop punt van uw cluster. Zie [SSH gebruiken met hdinsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over het gebruik van SSH met hdinsight-clusters.
  
2. Kopieer een voorbeeld bestand, mysamplefile.csv, naar de/share-map.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Schakel over naar R Studio of een andere R-console en schrijf R-code om het naam knooppunt in te stellen op de **standaard waarde** en de locatie van het bestand dat u wilt openen.  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

Alle verwijzingen naar de map en het bestand verwijzen naar het opslag account `wasbs://container1@storage1.blob.core.windows.net` . Dit is het **standaard opslag account** dat is gekoppeld aan het HDInsight-cluster.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>De extra opslag gebruiken met ML-Services in HDInsight

Stel nu dat u een bestand met de naam mysamplefile1.csv wilt verwerken dat zich bevindt in de map/private van **container2** in **storage2**.

In uw R-code wijst u de verwijzing naar het naam knooppunt naar het **storage2** -opslag account.

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

Alle directory-en bestands verwijzingen verwijzen nu naar het opslag account `wasbs://container2@storage2.blob.core.windows.net` . Dit is het **naam knooppunt** dat u hebt opgegeven.

Configureer de `/user/RevoShare/<SSH username>` Directory op **storage2** als volgt:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-gen1-with-ml-services-cluster"></a>Azure Data Lake Storage Gen1 gebruiken met een cluster van ML Services

Als u Data Lake Storage Gen1 met uw HDInsight-cluster wilt gebruiken, moet u uw cluster toegang geven tot elke Azure Data Lake Storage Gen1 die u wilt gebruiken. Zie [een hdinsight-cluster maken met Data Lake Storage Gen1 Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)voor instructies over het gebruik van de Azure Portal om een hdinsight-cluster te maken met een Azure data Lake Storage gen1 als standaard opslag of als extra opslag ruimte.

Vervolgens gebruikt u de opslag in uw R-script net zoals u een secundair Azure Storage-account hebt, zoals beschreven in de vorige procedure.

### <a name="add-cluster-access-to-your-azure-data-lake-storage-gen1"></a>Cluster toegang toevoegen aan uw Azure Data Lake Storage Gen1

U opent Data Lake Storage Gen1 met behulp van een Service-Principal Azure Active Directory (Azure AD) die is gekoppeld aan uw HDInsight-cluster.

1. Wanneer u een HDInsight-cluster maakt, selecteert u **cluster Azure AD-identiteit** op het tabblad **gegevens bron** .

2. Selecteer in het dialoog venster **cluster Azure AD-identiteit** onder **Select AD-Service-Principal** de optie **nieuwe maken**.

Wanneer u de service-principal een naam geeft en er een wacht woord voor maakt, klikt u op **ADLS toegang beheren** om de service-principal te koppelen aan uw data Lake Storage.

Het is ook mogelijk om cluster toegang toe te voegen aan een of meer Data Lake Storage gen1-accounts na het maken van het cluster. Open het Azure Portal item voor een Data Lake Storage Gen1 en ga naar **Data Explorer > toegang > toevoegen**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Toegang tot Data Lake Storage Gen1 vanuit ML Services op HDInsight

Zodra u toegang hebt gekregen tot Data Lake Storage Gen1, kunt u de opslag in ML Services-cluster op HDInsight gebruiken op dezelfde manier als een secundair Azure Storage-account. Het enige verschil is dat het voor voegsel **wasbs://** verandert in **ADL://** als volgt:

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

De volgende opdrachten worden gebruikt voor het configureren van de Data Lake Storage Gen1 met de RevoShare-map en het toevoegen van het voor beeld. CSV-bestand uit het vorige voor beeld:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>Azure File Storage gebruiken met ML-Services in HDInsight

Er is ook een handige optie voor gegevens opslag voor gebruik op het Edge-knoop punt met de naam [Azure files](https://azure.microsoft.com/services/storage/files/). Hiermee kunt u een Azure Storage bestands share koppelen aan het Linux-bestands systeem. Deze optie kan handig zijn voor het opslaan van gegevens bestanden, R-scripts en resultaat objecten die later mogelijk nodig zijn, met name wanneer het zinvol is om het systeem eigen bestandssysteem te gebruiken op het Edge-knoop punt in plaats van HDFS.

Een belang rijk voor deel van Azure Files is dat de bestands shares kunnen worden gekoppeld en gebruikt door elk systeem dat een ondersteund systeem heeft, zoals Windows of Linux. Het kan bijvoorbeeld worden gebruikt door een ander HDInsight-cluster dat u of iemand in uw team heeft, een Azure-VM of zelfs door een on-premises systeem. Zie voor meer informatie:

- [Azure File Storage gebruiken met Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Azure File Storage gebruiken in Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van het cluster van ML Services in HDInsight](r-server-overview.md)
- [Opties voor compute-context voor ML Services-cluster in HDInsight](r-server-compute-contexts.md)
- [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
