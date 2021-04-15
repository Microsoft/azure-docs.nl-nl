---
title: Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters
description: Meer informatie over het gebruik Azure Data Lake Storage Gen2 met Azure HDInsight clusters.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: ea9dc2627d5a6498f69ca8c61a9cac8089816886
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378581"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) is een cloudopslagservice die is toegewezen aan big data analytics, gebouwd [op Azure Blob Storage.](../storage/blobs/storage-blobs-introduction.md) Data Lake Storage Gen2 combineert de mogelijkheden van Azure Blob Storage en Azure Data Lake Storage Gen1. De resulterende service biedt functies van Azure Data Lake Storage Gen1 waaronder: semantiek van het bestandssysteem, beveiliging op map- en bestandsniveau en aanpassingsvermogen. Samen met de voordelige, gelaagde opslag, hoge beschikbaarheid en mogelijkheden voor herstel na noodgevallen vanuit Azure Blob Storage.

Zie Opslagopties vergelijken voor gebruik met clusters Data Lake Storage Gen2 een volledige vergelijking van de opties voor het [maken Azure HDInsight cluster.](hdinsight-hadoop-compare-storage-options.md)

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 beschikbaarheid

Data Lake Storage Gen2 is beschikbaar als opslagoptie voor bijna alle Azure HDInsight clustertypen als standaard en een extra opslagaccount. HBase kan echter slechts één account hebben met Data Lake Storage Gen2.

> [!Note]  
> Nadat u een Data Lake Storage Gen2 als uw primaire **opslagtype hebt** geselecteerd, kunt u geen Data Lake Storage Gen1 als extra opslag.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>HDInsight-clusters maken met Data Lake Storage Gen2

Gebruik de volgende koppelingen voor gedetailleerde instructies over het maken van HDInsight-clusters met toegang tot Data Lake Storage Gen2.

* [Portal gebruiken](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Azure CLI gebruiken](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* PowerShell wordt momenteel niet ondersteund voor het maken van een HDInsight-cluster met Azure Data Lake Storage Gen2.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Toegangsbeheer voor Data Lake Storage Gen2 in HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Welke soorten machtigingen worden Data Lake Storage Gen2 ondersteund?

Data Lake Storage Gen2 maakt gebruik van een model voor toegangsbeheer dat ondersteuning biedt voor zowel op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) als POSIX-achtige toegangsbeheerlijsten (ACL's). Data Lake Storage Gen1 ondersteunt alleen toegangsbeheerlijsten voor het beheren van de toegang tot gegevens.

Azure RBAC maakt gebruik van roltoewijzingen om effectief sets machtigingen toe te passen op gebruikers, groepen en service-principals voor Azure-resources. Deze Azure-resources zijn doorgaans beperkt tot resources op het hoogste niveau (bijvoorbeeld Azure Blob Storage-accounts). Voor Azure Blob Storage, en ook Data Lake Storage Gen2, is dit mechanisme uitgebreid naar de bestandssysteemresource.

Zie Op rollen gebaseerd toegangsbeheer van [Azure (Azure RBAC)](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)voor meer informatie over bestandsmachtigingen met Azure RBAC.

Zie Toegangsbeheerlijsten voor bestanden en mappen voor meer informatie over [bestandsmachtigingen met ACL's.](../storage/blobs/data-lake-storage-access-control.md)

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Hoe kan ik toegang tot mijn gegevens in Data Lake Storage Gen2?

De mogelijkheid van uw HDInsight-cluster om toegang te krijgen tot bestanden in Data Lake Storage Gen2 wordt beheerd via beheerde identiteiten. Een beheerde identiteit is een identiteit die is geregistreerd in Azure Active Directory (Azure AD) waarvan de referenties worden beheerd door Azure. Met beheerde identiteiten hoeft u geen service-principals te registreren in Azure AD. Of behoud referenties zoals certificaten.

Azure-services hebben twee typen beheerde identiteiten: door het systeem toegewezen en door de gebruiker toegewezen. HDInsight maakt gebruik van door de gebruiker toegewezen beheerde identiteiten voor toegang tot Data Lake Storage Gen2. Een `user-assigned managed identity` wordt gemaakt als een zelfstandige Azure-resource. Via een productieproces maakt Azure een identiteit in de Azure AD-tenant, die wordt vertrouwd door het gebruikte abonnement. Nadat de identiteit is gemaakt, kan deze worden toegewezen aan een of meer Azure-service-exemplaren.

De levenscyclus van een door de gebruiker toegewezen identiteit wordt afzonderlijk beheerd van de levenscyclus van de Azure Service-exemplaren waaraan de identiteit is toegewezen. Zie Wat zijn beheerde identiteiten voor Azure-resources? voor meer informatie [over beheerde identiteiten.](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hoe kan ik machtigingen instellen voor Azure AD-gebruikers om query's uit te voeren op gegevens in Data Lake Storage Gen2 met behulp van Hive of andere services?

Als u machtigingen wilt instellen voor gebruikers om gegevens op te vragen, gebruikt u Azure AD-beveiligingsgroepen als de toegewezen principal in ACL's. Wijs niet rechtstreeks machtigingen voor bestandstoegang toe aan afzonderlijke gebruikers of service-principals. Met Azure AD-beveiligingsgroepen om de stroom machtigingen te bepalen, kunt u gebruikers of service-principals toevoegen en verwijderen zonder ACL's opnieuw toe te voegen aan een volledige mapstructuur. U hoeft alleen de gebruikers toe te voegen aan of te verwijderen uit de juiste Azure AD-beveiligingsgroep. ACL's worden niet overgenomen, dus voor het opnieuw toevoegen van ACL's moet de ACL voor elk bestand en elke subdirectory worden bijgewerkt.

## <a name="access-files-from-the-cluster"></a>Bestanden openen vanuit het cluster

Er zijn verschillende manieren waarop u toegang hebt tot de bestanden in Data Lake Storage Gen2 vanuit een HDInsight-cluster.

* **De volledig gekwalificeerde naam gebruiken**. Met deze methode geeft u het volledige pad op naar het bestand dat u wilt openen.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **De verkorte padnotatie gebruiken**. Met deze methode vervangt u het pad naar de hoofdmap van het cluster door:

    ```
    abfs:///<file.path>/
    ```

* **Het relatieve pad gebruiken**. Met deze methode geeft u alleen het volledige relatieve pad op naar het bestand dat u wilt openen.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Voorbeelden van gegevenstoegang

Voorbeelden zijn gebaseerd op een [SSH-verbinding](./hdinsight-hadoop-linux-use-ssh-unix.md) met het hoofdknooppunt van het cluster. In de voorbeelden worden alle drie de URI-schema's gebruikt. Vervang `CONTAINERNAME` en door de relevante `STORAGEACCOUNT` waarden

#### <a name="a-few-hdfs-commands"></a>Enkele hdfs-opdrachten

1. Maak een bestand in de lokale opslag.

    ```bash
    touch testFile.txt
    ```

1. Maak directories op clusteropslag.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Gegevens kopiëren van lokale opslag naar clusteropslag.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Mapinhoud in clusteropslag weergeven.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Een Hive-tabel maken

Er worden drie bestandslocaties weergegeven voor illustratieve doeleinden. Gebruik slechts een van de vermeldingen voor de `LOCATION` werkelijke uitvoering.

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

* [Azure HDInsight integratie met Data Lake Storage Gen2 preview - ACL en beveiligingsupdate](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Zelfstudie: Gegevens extraheren, transformeren en laden met Interactive Query in Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
