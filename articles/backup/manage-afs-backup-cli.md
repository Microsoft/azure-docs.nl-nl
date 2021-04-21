---
title: Back-ups van Azure-bestands delen beheren met de Azure CLI
description: Meer informatie over het gebruik van de Azure CLI voor het beheren en bewaken van Azure-bestands shares die zijn back-up door Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: e389f5cde12734ef4bf0be4ecfba69ba33f5e030
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773599"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Back-ups van Azure-bestands delen beheren met de Azure CLI

De Azure CLI biedt een opdrachtregelervaring voor het beheren van Azure-resources. Het is een geweldig hulpprogramma voor het bouwen van aangepaste automatisering voor het gebruik van Azure-resources. In dit artikel wordt uitgelegd hoe u taken uitvoert voor het beheren en bewaken van de Azure-bestands shares die worden back-up door [Azure Backup](./backup-overview.md). U kunt deze stappen ook uitvoeren met de [Azure Portal.](https://portal.azure.com/)

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgenomen dat u al een back-up van een Azure-bestands share hebt gemaakt door [Azure Backup.](./backup-overview.md) Als u nog geen back-up hebt, zie [Back-ups](backup-afs-cli.md) maken van Azure-bestands shares met de CLI om back-ups voor uw bestands shares te configureren. Voor dit artikel gebruikt u de volgende resources:
   -  **Resourcegroep:** *azurefiles*
   -  **RecoveryServicesVault:** *azurefilesvault*
   -  **Opslagaccount:** *afsaccount*
   -  **Bestands share:** *azurefiles*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - Voor deze zelfstudie is versie 2.0.18 of hoger Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="monitor-jobs"></a>Taken controleren

Wanneer u back-up- of herstelbewerkingen activeert, maakt de back-upservice een taak voor tracering. Als u voltooide of momenteel lopende taken wilt bewaken, gebruikt u de cmdlet [az backup job list.](/cli/azure/backup/job#az_backup_job_list) Met de CLI kunt u ook een taak die momenteel wordt [uitgevoerd,](/cli/azure/backup/job#az_backup_job_stop) opschorten of [wachten totdat een taak is uitgevoerd.](/cli/azure/backup/job#az_backup_job_wait)

In het volgende voorbeeld wordt de status van back-uptaken voor de *AzureFilesvault* Recovery Services-kluis weergegeven:

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Beleid wijzigen

U kunt een back-upbeleid wijzigen om de back-upfrequentie of bewaartermijn te wijzigen met [az backup item set-policy.](/cli/azure/backup/item#az_backup_item_set_policy)

Als u het beleid wilt wijzigen, definieert u de volgende parameters:

* **--container-name:** de naam van het opslagaccount dat als host voor de bestands share wordt gebruikt. Gebruik de **opdracht** az backup container list **om de** naam of de gebruiksvriendelijke naam van uw [container op te](/cli/azure/backup/container#az_backup_container_list) halen.
* **--name:** de naam van de bestands share waarvoor u het beleid wilt wijzigen. Gebruik de **opdracht** az backup item list om de naam **of** de gebruiksvriendelijke naam van uw back-upitem [op te](/cli/azure/backup/item#az_backup_item_list) halen.
* **--policy-name:** de naam van het back-upbeleid dat u wilt instellen voor uw bestands share. U kunt [az backup policy list gebruiken om](/cli/azure/backup/policy#az_backup_policy_list) alle beleidsregels voor uw kluis weer te bieden.

In het volgende voorbeeld wordt het *back-upbeleid schedule2* voor de bestands share *azurefiles* in het *opslagaccount afsaccount.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

U kunt de vorige opdracht ook uitvoeren met behulp van de gebruiksvriendelijke namen voor de container en het item door de volgende twee extra parameters op te geven:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Het **kenmerk Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw wijzigingsbeleidsbewerking. Gebruik de cmdlet az [backup job show](/cli/azure/backup/job#az_backup_job_show) om de status van de taak bij te houden.

## <a name="stop-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare stoppen

Er zijn twee manieren om het beveiligen van Azure-bestandsshares te stoppen:

* Stop alle toekomstige back-uptaken *en verwijder* alle herstelpunten.
* Stop alle toekomstige back-uptaken, *maar laat de* herstelpunten staan.

Mogelijk zijn er kosten verbonden aan het behouden van de herstelpunten in de opslag, omdat de onderliggende momentopnamen die door de Azure Backup worden bewaard. Het voordeel van het verlaten van de herstelpunten is de optie om de bestands share later te herstellen, als u wilt. Zie de prijsinformatie voor informatie over de kosten voor het verlaten van de [herstelpunten.](https://azure.microsoft.com/pricing/details/storage/files) Als u ervoor kiest om alle herstelpunten te verwijderen, kunt u de bestands share niet herstellen.

Als u de beveiliging voor de bestands share wilt stoppen, definieert u de volgende parameters:

* **--container-name:** de naam van het opslagaccount dat als host voor de bestands share wordt gebruikt. Gebruik de **opdracht** [az backup container list](/cli/azure/backup/container#az_backup_container_list) om de naam of **de** gebruiksvriendelijke naam van uw container op te halen.
* **--item-name:** de naam van de bestands share waarvoor u de beveiliging wilt stoppen. Gebruik de **opdracht** az backup item list om de naam **of** de gebruiksvriendelijke naam van uw back-upitem [op te](/cli/azure/backup/item#az_backup_item_list) halen.

### <a name="stop-protection-and-retain-recovery-points"></a>Beveiliging stoppen en herstelpunten behouden

Als u de beveiliging wilt stoppen terwijl gegevens behouden blijven, gebruikt u de cmdlet [az backup protection disable.](/cli/azure/backup/protection#az_backup_protection_disable)

In het volgende voorbeeld wordt de beveiliging voor de *bestands share azurefiles* gestopt, maar worden alle herstelpunten bewaard.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

U kunt de vorige opdracht ook uitvoeren met behulp van de gebruiksvriendelijke naam voor de container en het item door de volgende twee extra parameters op te geven:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Het **kenmerk Naam** in de uitvoer komt overeen met de naam van de taak die door de back-upservice is gemaakt voor uw stopbeveiligingsbewerking. Gebruik de cmdlet az [backup job show](/cli/azure/backup/job#az_backup_job_show) om de status van de taak bij te houden.

### <a name="stop-protection-without-retaining-recovery-points"></a>Beveiliging stoppen zonder herstelpunten te behouden

Als u de beveiliging wilt stoppen zonder herstelpunten te behouden, gebruikt u de cmdlet [az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable) met de optie **delete-backup-data** ingesteld op **true**.

In het volgende voorbeeld wordt de beveiliging voor de *bestands share azurefiles* gestopt zonder herstelpunten te behouden.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

U kunt de vorige opdracht ook uitvoeren met behulp van de gebruiksvriendelijke naam voor de container en het item door de volgende twee extra parameters op te geven:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare hervatten

Als u de beveiliging voor een Azure-bestands share hebt gestopt, maar herstelpunten hebt behouden, kunt u de beveiliging later hervatten. Als u de herstelpunten niet behoudt, kunt u de beveiliging niet hervatten.

Als u de beveiliging voor de bestands share wilt hervatten, definieert u de volgende parameters:

* **--container-name:** de naam van het opslagaccount dat als host voor de bestands share wordt gebruikt. Gebruik de **opdracht** az backup container list **om de** naam of de gebruiksvriendelijke naam van uw [container op te](/cli/azure/backup/container#az_backup_container_list) halen.
* **--item-name:** de naam van de bestands share waarvoor u de beveiliging wilt hervatten. Gebruik de **opdracht** az backup item list om de naam **of** de gebruiksvriendelijke naam van uw back-upitem [op te](/cli/azure/backup/item#az_backup_item_list) halen.
* **--policy-name:** de naam van het back-upbeleid waarvoor u de beveiliging voor de bestands share wilt hervatten.

In het volgende voorbeeld wordt de cmdlet [az backup protection resume](/cli/azure/backup/protection#az_backup_protection_resume) gebruikt om de beveiliging voor de bestands share *azurefiles* te hervatten met behulp van *het back-upbeleid schedule1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

U kunt de vorige opdracht ook uitvoeren met behulp van de gebruiksvriendelijke naam voor de container en het item door de volgende twee extra parameters op te geven:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Het **kenmerk Naam** in de uitvoer komt overeen met de naam van de taak die door de back-upservice is gemaakt voor de bewerking voor het hervatten van de beveiliging. Gebruik de cmdlet az [backup job show](/cli/azure/backup/job#az_backup_job_show) om de status van de taak bij te houden.

## <a name="unregister-a-storage-account"></a>Registratie van een opslagaccount ongedaan maken

Als u uw bestands shares [in](#stop-protection-on-a-file-share) een bepaald opslagaccount wilt beveiligen met behulp van een andere Recovery Services-kluis, stopt u eerst de beveiliging voor alle bestands shares in dat opslagaccount. Vervolgens moet u de registratie van het account ongedaan maken bij de Recovery Services-kluis die momenteel wordt gebruikt voor beveiliging.

U moet een containernaam verstrekken om de registratie van het opslagaccount ongedaan te maken. Gebruik de **opdracht** [az backup container list](/cli/azure/backup/container#az_backup_container_list) om de naam of **de** gebruiksvriendelijke naam van uw container op te halen.

In het volgende voorbeeld wordt de registratie van het *opslagaccount afsaccount* bij *azurefilesvault* ongedaan gemaakt met behulp van de cmdlet [az backup container unregister.](/cli/azure/backup/container#az_backup_container_unregister)

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

U kunt de vorige cmdlet ook uitvoeren met behulp van de gebruiksvriendelijke naam voor de container door de volgende aanvullende parameter op te geven:

* **--backup-management-type:** *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Volgende stappen

Zie Problemen met back-ups van [Azure-bestands shares oplossen voor meer informatie.](troubleshoot-azure-files.md)
