---
title: Gegevens opvragen uit HDFS-compatibele Azure-opslag - Azure HDInsight
description: Meer informatie over het opvragen van gegevens uit Azure Storage en Azure Data Lake Storage om de resultaten van uw analyse op te slaan.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: cedc0ff1b3c2aa64f32445eabc800748a753981d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945430"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Azure-opslag gebruiken met Azure HDInsight-clusters

U kunt gegevens opslaan in [Azure Blob Storage](../storage/common/storage-introduction.md), [Azure data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md)of [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md). Of een combi natie van deze opties. Met deze opslag opties kunt u HDInsight-clusters die worden gebruikt voor berekeningen, veilig verwijderen zonder dat er gebruikers gegevens verloren gaan.

Apache Hadoop ondersteunt een principe van het standaard bestandssysteem. Het standaardbestandssysteem impliceert een standaardschema en instantie. De toepassing kan ook worden gebruikt om relatieve paden om te zetten. Tijdens het maken van het HDInsight-cluster kunt u een BLOB-container in Azure Storage opgeven als het standaard bestandssysteem. Of met HDInsight 3,6 kunt u Azure Blob-opslag of Azure Data Lake Storage Gen1/Azure Data Lake Storage Gen2 als het standaard bestandssysteem met een paar uitzonde ringen selecteren. Zie [Beschik baarheid voor HDInsight-cluster](./hdinsight-hadoop-use-data-lake-storage-gen1.md#availability-for-hdinsight-clusters)voor de ondersteuning van het gebruik van data Lake Storage gen1 als zowel de standaard als de gekoppelde opslag.

In dit artikel wordt uitgelegd hoe Azure Storage werkt met HDInsight-clusters. 
* Zie [Azure data Lake Storage gen1 gebruiken met Azure hdinsight-clusters](./hdinsight-hadoop-use-data-lake-storage-gen1.md)voor meer informatie over de werking van data Lake Storage gen1 met hdinsight-clusters.
* Zie [Azure data Lake Storage Gen2 gebruiken met Azure hdinsight-clusters](./hdinsight-hadoop-use-data-lake-storage-gen2.md)voor meer informatie over de werking van data Lake Storage Gen2 met hdinsight-clusters.
* Zie [Apache Hadoop clusters maken in HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een HDInsight-cluster.

> [!IMPORTANT]  
> Type opslag account **BlobStorage** kan alleen worden gebruikt als secundaire opslag voor HDInsight-clusters.

| Opslagaccounttype | Ondersteunde services | Ondersteunde prestatielagen |Geen ondersteunde prestatie lagen| Ondersteunde toegangslagen |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (algemeen gebruik v2)  | Blob     | Standard                    |Premium| Dynamisch, statisch en archiefopslag\*   |
| Storage (algemene doeleinden v1)   | Blob     | Standard                    |Premium| N.v.t.                    |
| BlobStorage                    | Blob     | Standard                    |Premium| Dynamisch, statisch en archiefopslag\*   |

Het is niet raadzaam om de standaard BLOB-container te gebruiken voor het opslaan van Bedrijfs gegevens. Het is een goede gewoonte om de standaard-blobcontainer na ieder gebruik te verwijderen om de opslagkosten te verlagen. De standaard container bevat toepassings-en systeem Logboeken. Breng de logboeken over naar een andere locatie voordat u de container verwijdert.

Het delen van één BLOB-container als het standaard bestandssysteem voor meerdere clusters wordt niet ondersteund.

> [!NOTE]  
> De Access-laag voor archivering is een offline laag met een aantal latentie voor het ophalen van uur en wordt niet aanbevolen voor gebruik met HDInsight. Zie [Access-laag archiveren](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)voor meer informatie.

## <a name="access-files-from-within-cluster"></a>Toegang tot bestanden in het cluster

Er zijn verschillende manieren om toegang te krijgen tot de bestanden in Data Lake Storage vanuit een HDInsight-cluster. Het URI-schema biedt niet-versleutelde toegang (met *wasb:* voor voegsel) en TLS-versleutelde toegang (met *wasbs*). Waar mogelijk kunt u het beste *wasbs* gebruiken, zelfs voor de toegang tot gegevens die zich in dezelfde regio in Azure bevinden.

* **De volledig gekwalificeerde naam gebruiken**. Met deze methode geeft u het volledige pad op naar het bestand dat u wilt openen.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **De verkorte padnotatie gebruiken**. Met deze methode vervangt u het pad naar de hoofdmap van het cluster met:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Het relatieve pad gebruiken**. Met deze methode geeft u alleen het volledige relatieve pad op naar het bestand dat u wilt openen.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Voor beelden van gegevens toegang

Voor beelden zijn gebaseerd op een [SSH-verbinding](./hdinsight-hadoop-linux-use-ssh-unix.md) met het hoofd knooppunt van het cluster. De voor beelden gebruiken alle drie de URI-schema's. Vervangen `CONTAINERNAME` en `STORAGEACCOUNT` door de relevante waarden

#### <a name="a-few-hdfs-commands"></a>Enkele hdfs-opdrachten

1. Maak een bestand op lokale opslag.

    ```bash
    touch testFile.txt
    ```

1. Maak mappen in de cluster opslag.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Gegevens uit de lokale opslag kopiëren naar de cluster opslag.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Mapinhoud weer geven in cluster opslag.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Als u buiten HDInsight met blobs werkt, zullen de meeste hulpprogramma's de indeling WASB niet herkennen en wordt er in plaats daarvan een standaardpadindeling verwacht, zoals `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Een Hive-tabel maken

Er worden drie bestands locaties weer gegeven voor illustratie doeleinden. Gebruik slechts één van de vermeldingen voor daad werkelijke uitvoering `LOCATION` .

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>Toegang tot bestanden buiten het cluster

Micro soft biedt de volgende hulpprogram ma's voor het werken met Azure Storage:

| Hulpprogramma | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure-CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Pad naar opslag van Ambari identificeren

* Als u het volledige pad naar het geconfigureerde standaard archief wilt identificeren, gaat u naar:

    **HDFS**  >  **Configuratie** en voer `fs.defaultFS` in het vak Filter invoer.

* Als u wilt controleren of de wasb-opslag is geconfigureerd als secundaire opslag, gaat u naar:

    **HDFS**  >  **Configuratie** en voer `blob.core.windows.net` in het vak Filter invoer.

Zie [de standaard opslag ophalen](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage)om het pad op te halen met behulp van Ambari rest API.

## <a name="blob-containers"></a>Blobcontainers

Als u blobs wilt gebruiken, maakt u eerst een [Azure Storage-account](../storage/common/storage-account-create.md). Als onderdeel van deze stap geeft u een Azure-regio op waar het opslag account wordt gemaakt. Het cluster en het opslagaccount moeten worden gehost in dezelfde regio. De Hive-metastore SQL Server-Data Base en Apache Oozie-meta Store SQL Server-Data Base moeten zich in dezelfde regio bevinden.

Elke blob die u maakt, behoort tot een container in uw Azure Storage-account, ongeacht de locatie van de blob. Deze container kan een bestaande BLOB zijn die buiten HDInsight is gemaakt. Of het kan een container zijn die is gemaakt voor een HDInsight-cluster.

In de standaard blobcontainer worden clusterspecifieke gegevens opgeslagen, zoals taakgeschiedenis en logboeken. Deel een standaard blob-container niet met meerdere HDInsight-clusters. Deze actie kan de taak geschiedenis beschadigen. Het is raadzaam om voor elk cluster een andere container te gebruiken. Plaats gedeelde gegevens op een gekoppeld opslag account dat is opgegeven voor alle relevante clusters in plaats van het standaard opslag account. Zie [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie over het configureren van gekoppelde opslagaccounts. U kunt een standaardopslagcontainer echter opnieuw gebruiken nadat het oorspronkelijke HDInsight-cluster is verwijderd. Voor HBase-clusters kunt u het schema en de gegevens van de HBase-tabel blijven door een nieuw HBase-cluster te maken met behulp van de standaard-BLOB-container die wordt gebruikt door een verwijderd HBase-cluster

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Extra opslagaccounts gebruiken

Tijdens het maken van een HDInsight-cluster geeft u het Azure Storage-account op dat u ermee wilt koppelen. U kunt ook extra opslag accounts van hetzelfde Azure-abonnement of andere Azure-abonnementen toevoegen tijdens het maken of nadat een cluster is gemaakt. Zie [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md) voor instructies over het toevoegen van extra opslagaccounts.

> [!WARNING]  
> Het gebruik van een extra opslagaccount op een andere locatie dan het HDInsight-cluster wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u HDFS-compatibele Azure-opslag kunt gebruiken met HDInsight. Met deze opslag kunt u flexibele, langdurige, archiverings oplossingen voor gegevens verzameling bouwen en HDInsight gebruiken om de informatie in de opgeslagen gestructureerde en ongestructureerde gegevens te ontgrendelen.

Zie voor meer informatie:

* [Snelstartgids: Apache Hadoop cluster maken](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Zelf studie: HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md)
* [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md)
* [Zelfstudie: Gegevens extraheren, transformeren en laden met Interactive Query in Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Handtekeningen voor gedeelde toegang van Azure Storage gebruiken om de toegang tot gegevens te beperken met HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)