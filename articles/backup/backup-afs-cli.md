---
title: Een back-up maken van Azure-bestands shares met Azure CLI
description: Meer informatie over het gebruik van Azure CLI om een back-up te maken van Azure-bestands shares in de Recovery Services-kluis
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: a5f7472c511a5a50415a6ceb47497dd6f4f1e60b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773617"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>Een back-up maken van Azure-bestands shares met Azure CLI

De Azure-opdrachtregelinterface (CLI) biedt een opdrachtregelervaring voor het beheren van Azure-resources. Het is een geweldig hulpprogramma voor het bouwen van aangepaste automatisering voor het gebruik van Azure-resources. In dit artikel wordt beschreven hoe u een back-up van Azure-bestands shares kunt maken met Azure CLI. U kunt deze stappen ook uitvoeren met [Azure PowerShell](./backup-azure-afs-automation.md) of in [Azure Portal](backup-afs.md).

Aan het einde van deze zelfstudie leert u hoe u de onderstaande bewerkingen kunt uitvoeren met Azure CLI:

* Een Recovery Services-kluis maken
* Back-up inschakelen voor Azure-bestands shares
* Een back-up op aanvraag activeren voor bestands shares

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0.18 of hoger Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Services-kluis is een entiteit die u een geconsolideerde weergave en beheermogelijkheid biedt voor alle back-upitems. Wanneer de back-uptaak voor een beveiligde resource wordt uitgevoerd, wordt er binnen de Recovery Services-kluis een herstelpunt gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen.

Volg deze stappen om een Recovery Services-kluis te maken:

1. Een kluis wordt in een resourcegroep geplaatst. Als u geen bestaande resourcegroep hebt, maakt u een nieuwe met [az group create.](/cli/azure/group#az_group_create) In deze zelfstudie maken we de nieuwe resourcegroep *azurefiles* in de regio VS - oost.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. Gebruik de [cmdlet az backup vault create](/cli/azure/backup/vault#az_backup_vault_create) om de kluis te maken. Geef dezelfde locatie voor de kluis op als die voor de resourcegroep is gebruikt.

    In het volgende voorbeeld wordt een Recovery Services-kluis met de *naam azurefilesvault gemaakt* in de regio VS - oost.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Back-up inschakelen voor Azure-bestands shares

In deze sectie wordt ervan uitgenomen dat u al een Azure-bestands share hebt waarvoor u een back-up wilt configureren. Als u er nog geen hebt, maakt u een Azure-bestands share met behulp van [de opdracht az storage share create.](/cli/azure/storage/share#az_storage_share_create)

Als u back-ups wilt inschakelen voor bestands shares, moet u een beveiligingsbeleid maken dat definieert wanneer een back-up job wordt uitgevoerd en hoe lang herstelpunten worden opgeslagen. U kunt een back-upbeleid maken met de cmdlet [az backup policy create.](/cli/azure/backup/policy#az_backup_policy_create)

In het volgende voorbeeld wordt de cmdlet [az backup protection enable-for-azurefileshare](/cli/azure/backup/protection#az_backup_protection_enable_for_azurefileshare) gebruikt om back-ups in te stellen voor de *bestandsshare azurefiles* in het *opslagaccount afsaccount* met behulp van het back-upbeleid schema *1:*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Het **kenmerk Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw **back-upbewerking inschakelen.** Gebruik de cmdlet [az backup job show](/cli/azure/backup/job#az_backup_job_show) om de status van de taak bij te houden.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Een back-up op aanvraag activeren voor een bestands share

Als u een back-up op aanvraag voor uw bestands share wilt activeren in plaats van te wachten tot het back-upbeleid de taak op het geplande tijdstip heeft uitgevoerd, gebruikt u de cmdlet [az backup protection backup-now.](/cli/azure/backup/protection#az_backup_protection_backup_now)

U moet de volgende parameters definiëren om een back-up op aanvraag te activeren:

* **--container-name** is de naam van het opslagaccount dat als host voor de bestands share wordt gebruikt. Gebruik de **opdracht** [az backup container list](/cli/azure/backup/container#az_backup_container_list) om de naam of **de** gebruiksvriendelijke naam van uw container op te halen.
* **--item-name** is de naam van de bestands share waarvoor u een back-up op aanvraag wilt activeren. Gebruik de **opdracht** [az backup item list](/cli/azure/backup/item#az_backup_item_list) **om** de naam of de gebruiksvriendelijke naam van uw back-upitem op te halen.
* **--retain-until** geeft de datum aan tot wanneer u het herstelpunt wilt behouden. De waarde moet worden ingesteld in UTC-tijdnotatie (dd-mm-yyyy).

In het volgende voorbeeld wordt een back-up op aanvraag voor de *bestandsshare azurefiles* in het *opslagaccount afsaccount* met retentie tot *20-01-2020 activeert.*

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Het **kenmerk Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw back-upbewerking op aanvraag. Gebruik de cmdlet az [backup job show](/cli/azure/backup/job#az_backup_job_show) om de status van een taak bij te houden.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [herstellen van Azure-bestands shares met CLI](restore-afs-cli.md)
* Meer informatie over het [beheren van back-ups van Azure-bestands delen met CLI](manage-afs-backup-cli.md)
