---
title: Azure Data Lake Analytics beheren met Azure CLI
description: In dit artikel wordt beschreven hoe u de Azure CLI gebruikt om Data Lake Analytics-taken, gegevens bronnen & gebruikers te beheren.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 01/29/2018
ms.openlocfilehash: 19b471d85a52fe38b72ad55847d022fb56b3c4f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220921"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Azure Data Lake Analytics beheren met de Azure-opdracht regel interface (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Meer informatie over het beheren van Azure Data Lake Analytics accounts, gegevens bronnen, gebruikers en taken met behulp van de Azure CLI. Klik op het tabblad selecteren hierboven voor een overzicht van de beheer onderwerpen met andere hulpprogram ma's.

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u beschikken over de volgende resources:

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Azure CLI. Zie [Azure CLI installeren en configureren](/cli/azure/install-azure-cli).

  - Download en installeer de **pre-release** [Azure CLI-hulpprogramma’s](https://github.com/MicrosoftBigData/AzureDataLake/releases) om deze demo te voltooien.

- Verificatie met behulp van de `az login` opdracht en selecteer het abonnement dat u wilt gebruiken. Zie [Verbinding maken met een Azure-abonnement met Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie over verificatie met een werk- of schoolaccount.

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   U hebt nu toegang tot de Data Lake Analytics-en Data Lake Store opdrachten. Voer de volgende opdracht uit om de Data Lake Store-en Data Lake Analytics-opdrachten weer te geven:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Accounts beheren

Voordat u een Data Lake Analytics-taak uitvoert, moet u een Data Lake Analytics-account hebben. In tegens telling tot Azure HDInsight betaalt u niet voor een Analytics-account wanneer er geen taak wordt uitgevoerd. U betaalt alleen voor de tijd waarop een taak wordt uitgevoerd.  Zie [Azure data Lake Analytics Overview](data-lake-analytics-overview.md)voor meer informatie.

### <a name="create-accounts"></a>Accounts maken

Voer de volgende opdracht uit om een Data Lake-account te maken.

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Accounts bijwerken

Met de volgende opdracht worden de eigenschappen van een bestaand Data Lake Analytics account bijgewerkt

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Lijst van accounts

Data Lake Analytics accounts in een specifieke resource groep weer geven

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Details van een account ophalen

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Een account verwijderen

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Gegevensbronnen beheren

Data Lake Analytics ondersteunt momenteel de volgende twee gegevens bronnen:

- [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
- [Azure Storage](../storage/common/storage-introduction.md)

Wanneer u een Analytics-account maakt, moet u een Azure Data Lake Storage-account aanwijzen als het standaard opslag account. De standaard Data Lake Storage-account wordt gebruikt voor het opslaan van meta gegevens van taken en taak controle Logboeken. Nadat u een Analytics-account hebt gemaakt, kunt u aanvullende Data Lake Storage accounts en/of Azure Storage account toevoegen.

### <a name="find-the-default-data-lake-store-account"></a>De standaard Data Lake Store account zoeken

U kunt het standaard Data Lake Store-account weer geven dat wordt gebruikt door de opdracht uit te voeren `az dla account show` . De standaard accountnaam wordt vermeld onder de eigenschap defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Extra Blob Storage-accounts toevoegen

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Alleen de korte namen van Blob Storage worden ondersteund. Gebruik geen FQDN, bijvoorbeeld ' myblob.blob.core.windows.net '.
>

### <a name="add-additional-data-lake-store-accounts"></a>Aanvullende Data Lake Store-accounts toevoegen

Met de volgende opdracht wordt het opgegeven Data Lake Analytics-account bijgewerkt met een aanvullend Data Lake Store-account:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Bestaande gegevens bron bijwerken

Een bestaande sleutel voor het Blob-opslag account bijwerken:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Gegevens bronnen weer geven

De Data Lake Store-accounts weer geven:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Het Blob Storage-account weer geven:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Scherm opname van Azure C L I met de informatie ' dataLakeStoreAccounts: ' gemarkeerd.](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Gegevens bronnen verwijderen

Een Data Lake Store account verwijderen:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Een Blob Storage-account verwijderen:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Taken beheren

U moet een Data Lake Analytics-account hebben voordat u een taak kunt maken.  Zie [Manage data Lake Analytics accounts](#manage-accounts)(Engelstalig) voor meer informatie.

### <a name="list-jobs"></a>Taken weer geven

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Gegevens bron Data Lake Analytics lijst](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Taak Details ophalen

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Taken verzenden

> [!NOTE]
> De standaard prioriteit van een taak is 1000 en de standaard graad van parallellisme voor een taak is 1.
>
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Taken annuleren

Gebruik de opdracht list om de taak-ID te vinden en gebruik vervolgens annuleren om de taak te annuleren.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Pijplijnen en herhalingen

### <a name="get-information-about-pipelines-and-recurrences"></a>Meer informatie over pijplijnen en herhalingen

Gebruik de `az dla job pipeline`-opdrachten om de pijplijngegevens te zien voor eerder verzonden taken.

```azurecli
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Gebruik de `az dla job recurrence`-opdrachten om de herhalingsgegevens te zien voor eerder verzonden taken.

```azurecli
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Aan de slag met Data Lake Analytics met Azure Portal](data-lake-analytics-get-started-portal.md)
- [Azure Data Lake Analytics beheren met Azure Portal](data-lake-analytics-manage-use-portal.md)
- [Azure Data Lake Analytics-taken bewaken en problemen oplossen met Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
