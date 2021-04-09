---
title: Apache Hadoop clusters beheren met Power shell-Azure HDInsight
description: Meer informatie over het uitvoeren van beheer taken voor de Apache Hadoop clusters in HDInsight met behulp van Azure PowerShell.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: c8581c2288eacb47fb9475a3b0a584434c8604f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942815"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Apache Hadoop-clusters in HDInsight beheren met behulp van Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell kan worden gebruikt om de implementatie en het beheer van uw workloads in azure te beheren en te automatiseren. In dit artikel vindt u informatie over het beheren van [Apache Hadoop](https://hadoop.apache.org/) clusters in azure HDInsight met behulp van de Azure PowerShell AZ-module. Zie de [verwijzing AZ. HDInsight](/powershell/module/az.hdinsight)voor de lijst met de Hdinsight Power shell-cmdlets.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De [Az-module](/powershell/azure/) van PowerShell geïnstalleerd.

## <a name="create-clusters"></a>Clusters maken

Zie [op Linux gebaseerde clusters maken in HDInsight met behulp van Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Clusters weergeven

Gebruik de volgende opdracht om alle clusters in het huidige abonnement weer te geven:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Cluster weer geven

Gebruik de volgende opdracht om details weer te geven van een specifiek cluster in het huidige abonnement:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Clusters verwijderen

Gebruik de volgende opdracht om een cluster te verwijderen:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

U kunt ook een cluster verwijderen door de resource groep te verwijderen die het cluster bevat. Als u een resource groep verwijdert, worden alle resources in de groep verwijderd, inclusief het standaard opslag account.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Clusters schalen

Met de functie voor het schalen van clusters kunt u het aantal worker-knoop punten wijzigen dat wordt gebruikt door een cluster dat wordt uitgevoerd in azure HDInsight zonder dat u het cluster opnieuw hoeft te maken. Als u de grootte van de Hadoop-cluster wilt wijzigen met behulp van Azure PowerShell, voert u de volgende opdracht uit vanaf een client computer:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Zie [HDInsight-clusters schalen](./hdinsight-scaling-best-practices.md)voor meer informatie over het schalen van clusters.

## <a name="update-http-user-credentials"></a>HTTP-gebruikers referenties bijwerken

[Set-AzHDInsightGatewayCredential](/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) stelt de gateway-http-referenties van een Azure HDInsight-cluster in.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Het standaard opslag account zoeken

Het volgende Power shell-script laat zien hoe u de standaard naam van het opslag account en de bijbehorende gegevens kunt ophalen:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>De resource groep zoeken

In de Resource Manager-modus maakt elk HDInsight-cluster deel uit van een Azure-resource groep.  De resource groep zoeken:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Taken verzenden

**MapReduce-taken verzenden**

Zie [de MapReduce-voor beelden uit HDInsight uitvoeren](hadoop/apache-hadoop-run-samples-linux.md).

**Apache Hive-taken verzenden**

Zie [Apache Hive Query's uitvoeren met behulp van Power shell](hadoop/apache-hadoop-use-hive-powershell.md).

**Apache Sqoop-taken verzenden**

Zie [Apache Sqoop gebruiken met HDInsight](hadoop/hdinsight-use-sqoop.md).

**Apache Oozie-taken verzenden**

Zie [Apache Oozie met Apache Hadoop gebruiken om een werk stroom in HDInsight te definiëren en uit te voeren](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Gegevens uploaden naar Azure Blob Storage

Zie [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Zie ook

* [AZ. HDInsight-cmdlets](/powershell/module/az.hdinsight/#hdinsight)
* [Apache Hadoop clusters in HDInsight beheren door gebruik te maken van de Azure Portal](hdinsight-administer-use-portal-linux.md)
* [HDInsight beheren met een opdracht regel interface](hdinsight-administer-use-command-line.md)
* [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Hadoop-taken via een programma indienen](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)