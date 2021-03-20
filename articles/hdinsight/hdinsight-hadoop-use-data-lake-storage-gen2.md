---
title: Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters
description: Meer informatie over het gebruik van Azure Data Lake Storage Gen2 met Azure HDInsight-clusters.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/24/2020
ms.openlocfilehash: 2bbfbd2d953ea663453f0092ff366e95f6dd5ea7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945368"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters

[Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) is een service voor Cloud opslag die is toegewezen aan Big Data Analytics, gebouwd op [Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md). Data Lake Storage Gen2 combineert de mogelijkheden van Azure Blob-opslag en Azure Data Lake Storage Gen1. De resulterende service biedt functies van Azure Data Lake Storage Gen1, waaronder: bestandssysteem semantiek, beveiliging op Directory-en bestands niveau en aanpassings mogelijkheden. Samen met de lage kosten, gelaagde opslag, maximale Beschik baarheid en herstel na nood gevallen van Azure Blob-opslag.

Zie [opslag opties vergelijken voor gebruik met Azure HDInsight-clusters](hdinsight-hadoop-compare-storage-options.md)voor een volledige vergelijking van de opties voor het maken van clusters met behulp van data Lake Storage Gen2.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Beschik baarheid Data Lake Storage Gen2

Data Lake Storage Gen2 is beschikbaar als een opslag optie voor bijna alle Azure HDInsight-cluster typen als standaard en als een extra opslag account. HBase kan echter slechts één account hebben met Data Lake Storage Gen2.

> [!Note]  
> Nadat u Data Lake Storage Gen2 als uw **primaire opslag type** selecteert, kunt u een Data Lake Storage gen1 als extra opslag ruimte niet selecteren.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>HDInsight-clusters maken met behulp van Data Lake Storage Gen2

Gebruik de volgende koppelingen voor gedetailleerde instructies over het maken van HDInsight-clusters met toegang tot Data Lake Storage Gen2.

* [Portal gebruiken](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Azure CLI gebruiken](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* Power shell wordt momenteel niet ondersteund voor het maken van een HDInsight-cluster met Azure Data Lake Storage Gen2.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Toegangs beheer voor Data Lake Storage Gen2 in HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Welke soorten machtigingen ondersteunt Data Lake Storage Gen2?

Data Lake Storage Gen2 gebruikt een model voor toegangs beheer dat zowel op rollen gebaseerd toegangs beheer (Azure RBAC) als POSIX-achtige Acl's (toegangs beheer lijsten) ondersteunt. Data Lake Storage Gen1 ondersteunt alleen toegangs beheer lijsten voor het beheren van de toegang tot gegevens.

Azure RBAC gebruikt roltoewijzingen voor het effectief Toep assen van machtigingen sets voor gebruikers, groepen en service-principals voor Azure-resources. Normaal gesp roken zijn deze Azure-resources beperkt tot resources op het hoogste niveau (bijvoorbeeld Azure Blob Storage-accounts). Voor Azure Blob-opslag, en ook Data Lake Storage Gen2, is dit mechanisme uitgebreid naar de bestands systeem bron.

Zie voor meer informatie over bestands machtigingen met Azure RBAC [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Zie [toegangs beheer lijsten voor bestanden en mappen](../storage/blobs/data-lake-storage-access-control.md)voor meer informatie over bestands machtigingen met acl's.

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hoe kan ik de toegang tot mijn gegevens in Data Lake Storage Gen2 beheren?

De mogelijkheid van uw HDInsight-cluster voor toegang tot bestanden in Data Lake Storage Gen2 wordt beheerd via beheerde identiteiten. Een beheerde identiteit is een identiteit die is geregistreerd in Azure Active Directory (Azure AD) waarvan de referenties door Azure worden beheerd. Met beheerde identiteiten hoeft u geen service-principals te registreren in azure AD. Of behoud referenties zoals certificaten.

Azure-Services hebben twee typen beheerde identiteiten: systeem toegewezen en door de gebruiker toegewezen. HDInsight maakt gebruik van door de gebruiker toegewezen beheerde identiteiten voor toegang tot Data Lake Storage Gen2. A `user-assigned managed identity` wordt gemaakt als een zelfstandige Azure-resource. Via een productieproces maakt Azure een identiteit in de Azure AD-tenant, die wordt vertrouwd door het gebruikte abonnement. Nadat de identiteit is gemaakt, kan deze worden toegewezen aan een of meer Azure-service-exemplaren.

De levenscyclus van een door de gebruiker toegewezen identiteit wordt afzonderlijk beheerd van de levenscyclus van de Azure Service-exemplaren waaraan de identiteit is toegewezen. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hoe kan ik machtigingen instellen voor Azure AD-gebruikers om gegevens op te vragen in Data Lake Storage Gen2 met behulp van Hive of andere services?

Als u machtigingen voor gebruikers wilt instellen om gegevens op te vragen, gebruikt u Azure AD-beveiligings groepen als de toegewezen Principal in Acl's. Wijs niet rechtstreeks machtigingen voor bestands toegang toe aan afzonderlijke gebruikers of service-principals. Met Azure AD-beveiligings groepen om de machtigings stroom te beheren, kunt u gebruikers of service-principals toevoegen en verwijderen zonder dat Acl's opnieuw worden toegepast op een volledige mapstructuur. U hoeft alleen de gebruikers toe te voegen aan of te verwijderen uit de juiste Azure AD-beveiligings groep. Acl's worden niet overgenomen. Als u Acl's opnieuw wilt Toep assen, moet u de toegangs beheer lijst voor elk bestand en elke submap bijwerken.

## <a name="access-files-from-the-cluster"></a>Bestanden openen vanuit het cluster

Er zijn verschillende manieren om toegang te krijgen tot de bestanden in Data Lake Storage Gen2 vanuit een HDInsight-cluster.

* **De volledig gekwalificeerde naam gebruiken**. Met deze methode geeft u het volledige pad op naar het bestand dat u wilt openen.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **De verkorte padnotatie gebruiken**. Met deze methode vervangt u het pad naar de hoofdmap van het cluster met:

    ```
    abfs:///<file.path>/
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
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Gegevens uit de lokale opslag kopiëren naar de cluster opslag.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Mapinhoud weer geven in cluster opslag.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

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
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Volgende stappen

* [Integratie van Azure HDInsight met Data Lake Storage Gen2-voor beeld-ACL en beveiligings update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Zelfstudie: Gegevens extraheren, transformeren en laden met Interactive Query in Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
