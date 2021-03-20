---
title: Back-ups van Azure-bestands shares beheren met de Azure CLI
description: Meer informatie over het gebruik van de Azure CLI voor het beheren en controleren van back-ups van Azure-bestands shares van Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5a8a785016845b836a102663a959e4b2f28696b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566449"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Back-ups van Azure-bestands shares beheren met de Azure CLI

De Azure CLI biedt een opdracht regel ervaring voor het beheer van Azure-resources. Het is een uitstekend hulp programma voor het maken van aangepaste automatisering om Azure-resources te gebruiken. In dit artikel wordt uitgelegd hoe u taken kunt uitvoeren voor het beheren en bewaken van de Azure-bestands shares waarvan een back-up is gemaakt door [Azure backup](./backup-overview.md). U kunt deze stappen ook uitvoeren met de [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al een back-up van een Azure-bestands share hebt gemaakt door [Azure backup](./backup-overview.md). Als u er nog geen hebt, raadpleegt u back-ups [maken van Azure-bestands shares met de CLI](backup-afs-cli.md) voor het configureren van back-ups voor uw bestands shares. Voor dit artikel gebruikt u de volgende bronnen:
   -  **Resource groep**: *Azure files*
   -  **RecoveryServicesVault**: *azurefilesvault*
   -  **Opslag account**: *afsaccount*
   -  **Bestands share**: *Azure files*
  
  [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
   - Voor deze zelfstudie is versie 2.0.18 of hoger Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="monitor-jobs"></a>Taken controleren

Wanneer u back-up-of herstel bewerkingen uitvoert, maakt de back-upservice een taak voor tracering. Als u voltooide of momenteel actieve taken wilt controleren, gebruikt u de cmdlet [AZ Backup Job List](/cli/azure/backup/job#az-backup-job-list) . Met de CLI kunt u ook [een taak die momenteel wordt uitgevoerd, onderbreken](/cli/azure/backup/job#az-backup-job-stop) of [wachten tot een taak is voltooid](/cli/azure/backup/job#az-backup-job-wait).

In het volgende voor beeld wordt de status van back-uptaken voor de *azurefilesvault* -Recovery Services kluis weer gegeven:

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

U kunt een back-upbeleid wijzigen om de back-upfrequentie of het Bewaar bereik te wijzigen met behulp van [AZ backup item set-Policy](/cli/azure/backup/item#az-backup-item-set-policy).

Als u het beleid wilt wijzigen, definieert u de volgende para meters:

* **--container naam**: de naam van het opslag account dat als host fungeert voor de bestands share. Als u de **naam** of **beschrijvende naam** van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--naam**: de naam van de bestands share waarvoor u het beleid wilt wijzigen. Als u de **naam** of **beschrijvende naam** van het back-upitem wilt ophalen, gebruikt u de opdracht [AZ back-upitems](/cli/azure/backup/item#az-backup-item-list) .
* **--beleids naam**: de naam van het back-upbeleid dat u wilt instellen voor de bestands share. U kunt de [lijst AZ back-upbeleid](/cli/azure/backup/policy#az-backup-policy-list) gebruiken om alle beleids regels voor uw kluis weer te geven.

In het volgende voor beeld wordt het *schedule2* -back-upbeleid ingesteld voor de *Azure files* -bestands share die aanwezig is in het *afsaccount* -opslag account.

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

U kunt ook de vorige opdracht uitvoeren door de beschrijvende namen voor de container en het item te gebruiken door de volgende twee extra para meters op te geven:

* **--back-upbeheer-type**: *opslag*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die door de back-upservice is gemaakt voor de bewerking van het wijzigings beleid. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="stop-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare stoppen

Er zijn twee manieren om het beveiligen van Azure-bestandsshares te stoppen:

* Stop alle toekomstige back-uptaken en *Verwijder* alle herstel punten.
* Stop alle toekomstige back-uptaken, maar *verlaat* de herstel punten.

Er zijn mogelijk kosten verbonden aan het verlaten van de herstel punten in de opslag, omdat de onderliggende moment opnamen die zijn gemaakt door Azure Backup, behouden blijven. Het voor deel van het verlaten van de herstel punten is de optie om de bestands share later te herstellen, indien gewenst. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/storage/files)voor meer informatie over de kosten voor het verlaten van de herstel punten. Als u ervoor kiest om alle herstel punten te verwijderen, kunt u de bestands share niet herstellen.

Als u de beveiliging voor de bestands share wilt stoppen, definieert u de volgende para meters:

* **--container naam**: de naam van het opslag account dat als host fungeert voor de bestands share. Als u de **naam** of **beschrijvende naam** van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--item-name**: de naam van de bestands share waarvoor u de beveiliging wilt stoppen. Als u de **naam** of **beschrijvende naam** van het back-upitem wilt ophalen, gebruikt u de opdracht [AZ back-upitems](/cli/azure/backup/item#az-backup-item-list) .

### <a name="stop-protection-and-retain-recovery-points"></a>Beveiliging stoppen en herstel punten behouden

Als u de beveiliging wilt stoppen tijdens het bewaren van gegevens, gebruikt u de cmdlet [AZ Backup Protection Disable](/cli/azure/backup/protection#az-backup-protection-disable) .

In het volgende voor beeld wordt de beveiliging van de *Azure files* -bestands share gestopt, maar worden alle herstel punten bewaard.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

U kunt ook de vorige opdracht uitvoeren door de beschrijvende naam voor de container en het item te gebruiken door de volgende twee extra para meters op te geven:

* **--back-upbeheer-type**: *opslag*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die wordt gemaakt door de back-upservice voor uw stop-beveiligings bewerking. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retaining-recovery-points"></a>Beveiliging stoppen zonder herstel punten te bewaren

Als u de beveiliging wilt stoppen zonder herstel punten te bewaren, gebruikt u de optie [AZ Backup Protection Disable](/cli/azure/backup/protection#az-backup-protection-disable) cmdlet with **Delete-Backup-Data** Option ingesteld op **True**.

In het volgende voor beeld wordt de beveiliging van de *Azure files* -bestands share stopgezet zonder dat er herstel punten worden bewaard.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

U kunt ook de vorige opdracht uitvoeren door de beschrijvende naam voor de container en het item te gebruiken door de volgende twee extra para meters op te geven:

* **--back-upbeheer-type**: *opslag*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare hervatten

Als u de beveiliging van een Azure-bestands share en behouden herstel punten hebt gestopt, kunt u de beveiliging later hervatten. Als u de herstel punten niet behoudt, kunt u de beveiliging niet hervatten.

Als u de beveiliging voor de bestands share wilt hervatten, definieert u de volgende para meters:

* **--container naam**: de naam van het opslag account dat als host fungeert voor de bestands share. Als u de **naam** of **beschrijvende naam** van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--item-name**: de naam van de bestands share waarvoor u de beveiliging wilt hervatten. Als u de **naam** of **beschrijvende naam** van het back-upitem wilt ophalen, gebruikt u de opdracht [AZ back-upitems](/cli/azure/backup/item#az-backup-item-list) .
* **--Policy name**: de naam van het back-upbeleid waarvoor u de beveiliging voor de bestands share wilt hervatten.

In het volgende voor beeld wordt de cmdlet [AZ Backup Protection resume](/cli/azure/backup/protection#az-backup-protection-resume) gebruikt om de beveiliging van de *Azure files* -bestands share te hervatten met behulp van het *schedule1* -back-upbeleid.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

U kunt ook de vorige opdracht uitvoeren door de beschrijvende naam voor de container en het item te gebruiken door de volgende twee extra para meters op te geven:

* **--back-upbeheer-type**: *opslag*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die door de back-upservice is gemaakt voor de bewerking voor het hervatten van de beveiliging. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="unregister-a-storage-account"></a>Registratie van een opslag account ongedaan maken

Als u uw bestands shares in een bepaald opslag account wilt beveiligen met behulp van een andere Recovery Services kluis, moet u eerst de [beveiliging stoppen voor alle bestands shares](#stop-protection-on-a-file-share) in dat opslag account. Hef de registratie van het account vervolgens op van de Recovery Services kluis die momenteel wordt gebruikt voor de beveiliging.

U moet een container naam opgeven om de registratie van het opslag account ongedaan te maken. Als u de **naam** of de **beschrijvende naam** van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .

In het volgende voor beeld wordt de registratie van de *afsaccount* -opslag account uit *azurefilesvault* ongedaan gemaakt met behulp van de cmdlet [AZ backup container unregister](/cli/azure/backup/container#az-backup-container-unregister) .

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

U kunt ook de vorige cmdlet uitvoeren met behulp van de beschrijvende naam voor de container door de volgende aanvullende para meter op te geven:

* **--back-upbeheer-type**: *opslag*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met back-ups van Azure-bestands shares oplossen](troubleshoot-azure-files.md)voor meer informatie.
