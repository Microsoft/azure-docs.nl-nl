---
title: Apache Hadoop & veilige overdrachtsopslag - Azure HDInsight
description: Informatie over het maken van HDInsight-clusters met Azure-opslag-accounts voor veilige overdracht.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: 22804015ebf0344c00e60c88f780fe22ba440b52
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774985"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop-clusters met beveiligde overdrachtsopslagaccounts in Azure HDInsight

De functie [Veilige overdracht vereist](../storage/common/storage-require-secure-transfer.md) verhoogt de beveiliging van uw Azure-opslagaccount door alle aanvragen naar uw account af te dwingen via een beveiligde verbinding. Deze functie en het wasbs-schema worden alleen ondersteund door HDInsight-clusterversie 3.6 of nieuwer.

> [!IMPORTANT]
> Het inschakelen van beveiligde opslagoverdracht na het maken van een cluster kan leiden tot fouten bij het gebruik van uw opslagaccount en wordt niet aanbevolen. Het is beter om een nieuw cluster te maken met behulp van een opslagaccount met beveiligde overdracht al ingeschakeld.

## <a name="storage-accounts"></a>Opslagaccounts

### <a name="azure-portal"></a>Azure Portal

Standaard wordt de eigenschap Veilige overdracht vereist ingeschakeld wanneer u een opslagaccount maakt in Azure Portal.

Zie Require secure transfer with Azure Portal (Veilige overdracht vereisen met Azure Portal) als u een bestaand opslagaccount wilt [Azure Portal.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)

### <a name="powershell"></a>PowerShell

Zorg ervoor dat voor de PowerShell-cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)de parameter `-EnableHttpsTrafficOnly` is ingesteld op `1` .

Zie Require secure transfer with PowerShell (Veilige overdracht vereisen met PowerShell) als u een bestaand opslagaccount wilt [bijwerken met PowerShell.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)

### <a name="azure-cli"></a>Azure CLI

Zorg ervoor dat voor de Azure [CLI-opdracht az storage account create](/cli/azure/storage/account#az_storage_account_create)de parameter is ingesteld op `--https-only` `true` .

Zie Require secure transfer with Azure CLI (Veilige overdracht vereisen met Azure CLI) als u een bestaand opslagaccount wilt [bijwerken met Azure CLI.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)

## <a name="add-additional-storage-accounts"></a>Extra opslagaccounts toevoegen

Er zijn verschillende opties om extra opslagaccounts met veilige overdracht toe te voegen:

* De Azure Resource Manager-sjabloon in de laatste sectie wijzigen.
* Een cluster maken met [Azure Portal](https://portal.azure.com) en een gekoppelde opslagaccount opgeven.
* Een scriptactie gebruiken om extra opslagaccounts met veilige overdracht toe te voegen aan een bestaand HDInsight-cluster. Zie [Extra opslagaccounts toevoegen aan HDInsight](hdinsight-hadoop-add-storage.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Het gebruik van Azure Storage (WASB) in plaats van [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) als standaardgegevensopslag
* Zie [Azure Storage gebruiken met HDInsight](hdinsight-hadoop-use-blob-storage.md) voor meer informatie over hoe HDInsight Azure Storage gebruikt.
* Zie [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md) voor meer informatie over het uploaden van gegevens naar HDInsight.