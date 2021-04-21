---
title: Azure-bestands shares herstellen met de Azure CLI
description: Meer informatie over het gebruik van de Azure CLI om back-up te maken van Azure-bestands shares in de Recovery Services-kluis
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 2edc2281c29023bb8dfe0268f23eacfe081d413e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768502"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Azure-bestands shares herstellen met de Azure CLI

De Azure CLI biedt een opdrachtregelervaring voor het beheren van Azure-resources. Het is een geweldig hulpprogramma voor het bouwen van aangepaste automatisering voor het gebruik van Azure-resources. In dit artikel wordt uitgelegd hoe u een volledige bestands share of specifieke bestanden herstelt vanaf een herstelpunt dat is gemaakt door [Azure Backup](./backup-overview.md) met behulp van de Azure CLI. U kunt deze stappen ook uitvoeren met [Azure PowerShell](./backup-azure-afs-automation.md) of in [Azure Portal](backup-afs.md).

Aan het einde van dit artikel leert u hoe u de volgende bewerkingen kunt uitvoeren met de Azure CLI:

* Herstelpunten weergeven voor een back-up van een Azure-bestands share.
* Een volledige Azure-bestands share herstellen.
* Afzonderlijke bestanden of mappen herstellen.

>[!NOTE]
> Azure Backup ondersteunt nu het herstellen van meerdere bestanden of mappen naar de oorspronkelijke of een alternatieve locatie met behulp van Azure CLI. Raadpleeg de sectie [Meerdere bestanden of mappen herstellen naar de oorspronkelijke](#restore-multiple-files-or-folders-to-original-or-alternate-location) of alternatieve locatie van dit document voor meer informatie.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgenomen dat u al een Azure-bestands share hebt waar een back-up van is gemaakt door Azure Backup. Als u nog geen back-up hebt, zie [Back-ups](backup-afs-cli.md) maken van Azure-bestands shares met de CLI om een back-up voor uw bestands share te configureren. Voor dit artikel gebruikt u de volgende resources:

| Bestandsshare | Storage-account | Region | Details |
|---|---|---|---|
| *azurefiles* | *afsaccount* | EastUS | Oorspronkelijke bronback-up met behulp van Azure Backup |
| *azurefiles1* | *afaccount1* | EastUS | Doelbron die wordt gebruikt voor herstel naar een alternatieve locatie |

U kunt een vergelijkbare structuur voor uw bestands shares gebruiken om de verschillende typen herstel uit te proberen die in dit artikel worden uitgelegd.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - Voor deze zelfstudie is versie 2.0.18 of hoger Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Herstelpunten ophalen voor de Azure-bestands share

Gebruik de [cmdlet az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) om alle herstelpunten voor de back-up van de bestands share weer te geven.

In het volgende voorbeeld wordt de lijst met herstelpunten opgehaald voor de bestands share *azurefiles* in *het opslagaccount afsaccount.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

U kunt de vorige cmdlet ook uitvoeren met behulp van de gebruiksvriendelijke naam voor de container en het item door de volgende twee extra parameters op te geven:

* **--backup-management-type:** *azurestorage*
* **--workload-type:** *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

De resultatenset is een lijst met herstelpunten met tijd- en consistentiedetails voor elk herstelpunt.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Het **kenmerk Naam** in de uitvoer komt overeen met de naam van het herstelpunt dat kan worden gebruikt als een waarde voor de parameter **--rp-name** in herstelbewerkingen.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Herstel van volledige share met behulp van de Azure CLI

U kunt deze hersteloptie gebruiken om de volledige bestands share te herstellen op de oorspronkelijke of een alternatieve locatie.

Definieer de volgende parameters om herstelbewerkingen uit te voeren:

* **--container-name:** de naam van het opslagaccount dat als host voor de oorspronkelijke back-up van de bestands share wordt gebruikt. Gebruik de opdracht [az backup container list](/cli/azure/backup/container#az_backup_container_list) om de naam of de gebruiksvriendelijke naam van uw container op te halen.
* **--item-name:** de naam van de oorspronkelijke back-up van de bestands share die u wilt gebruiken voor de herstelbewerking. Gebruik de opdracht [az backup item list](/cli/azure/backup/item#az_backup_item_list) om de naam of de gebruiksvriendelijke naam van uw back-upitem op te halen.

### <a name="restore-a-full-share-to-the-original-location"></a>Een volledige share herstellen naar de oorspronkelijke locatie

Wanneer u herstelt naar een oorspronkelijke locatie, hoeft u geen doelgerelateerde parameters op te geven. Alleen **Conflict oplossen** moet worden opgegeven.

In het volgende voorbeeld wordt de cmdlet [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az_backup_restore_restore_azurefileshare) gebruikt, met de herstelmodus ingesteld op *originallocation* om de *bestandsshare azurefiles* op de oorspronkelijke locatie te herstellen. U gebruikt het herstelpunt 932883129628959823, dat u hebt verkregen in Herstelpunten ophalen voor de [Azure-bestands share](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Het **kenmerk Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw herstelbewerking. Gebruik de cmdlet az [backup job show](/cli/azure/backup/job#az_backup_job_show) om de status van de taak bij te houden.

### <a name="restore-a-full-share-to-an-alternate-location"></a>Een volledige share herstellen naar een alternatieve locatie

U kunt deze optie gebruiken om een bestands share te herstellen naar een alternatieve locatie en de oorspronkelijke bestands share te behouden. Geef de volgende parameters op voor herstel naar een alternatieve locatie:

* **--target-storage-account:** het opslagaccount waarop de inhoud van de back-up wordt hersteld. Het doelopslagaccount moet zich op dezelfde locatie bevinden als de kluis.
* **--target-file-share:** de bestands share binnen het doelopslagaccount waarop de inhoud van de back-up wordt hersteld.
* **--target-folder:** de map onder de bestands share waarop de gegevens worden hersteld. Als de inhoud van de back-up moet worden hersteld naar een hoofdmap, geeft u de waarden van de doelmap op als een lege tekenreeks.
* **--resolve-conflict:** Instructie als er een conflict is met de herstelde gegevens. Accepteert **overschrijven of** **overslaan.**

In het volgende voorbeeld wordt az backup restore restore  [restore-azurefileshare](/cli/azure/backup/restore#az_backup_restore_restore_azurefileshare) met de herstelmodus gebruikt als alternatieve locatie om de bestandsshare *azurefiles* in het *opslagaccount afsaccount* te herstellen naar de bestandsshare *azurefiles1 in* het *opslagaccount afaccount1.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Het **kenmerk Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw herstelbewerking. Gebruik de cmdlet az [backup job show](/cli/azure/backup/job#az_backup_job_show) om de status van de taak bij te houden.

## <a name="item-level-recovery"></a>Herstel op itemniveau

U kunt deze hersteloptie gebruiken om afzonderlijke bestanden of mappen op de oorspronkelijke of een alternatieve locatie te herstellen.

Definieer de volgende parameters om herstelbewerkingen uit te voeren:

* **--container-name:** de naam van het opslagaccount dat als host voor de oorspronkelijke back-up van de bestands share wordt gebruikt. Gebruik de opdracht [az backup container list](/cli/azure/backup/container#az_backup_container_list) om de naam of de gebruiksvriendelijke naam van uw container op te halen.
* **--item-name:** de naam van de oorspronkelijke back-up van de bestands share die u wilt gebruiken voor de herstelbewerking. Gebruik de opdracht [az backup item list](/cli/azure/backup/item#az_backup_item_list) om de naam of de gebruiksvriendelijke naam van uw back-upitem op te halen.

Geef de volgende parameters op voor de items die u wilt herstellen:

* **SourceFilePath:** het absolute pad van het bestand dat als tekenreeks moet worden hersteld in de bestands share. Dit pad is hetzelfde pad dat wordt gebruikt in de CLI-opdrachten [az storage file download](/cli/azure/storage/file#az_storage_file_download) of az storage file [show.](/cli/azure/storage/file#az_storage_file_show)
* **SourceFileType:** kies of een map of een bestand is geselecteerd. Accepteert **map** of **bestand**.
* **ResolveConflict:** Instructie als er een conflict is met de herstelde gegevens. Accepteert **overschrijven of** **overslaan.**

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Afzonderlijke bestanden of mappen herstellen naar de oorspronkelijke locatie

Gebruik de cmdlet [az backup restore restore-azurefiles](/cli/azure/backup/restore#az_backup_restore_restore_azurefiles) met de herstelmodus ingesteld op *originallocation* om specifieke bestanden of mappen te herstellen naar de oorspronkelijke locatie.

In het volgende voorbeeld wordt het *RestoreTest.txt* hersteld op de oorspronkelijke locatie: de *bestands share azurefiles.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Het **kenmerk Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw herstelbewerking. Gebruik de cmdlet az [backup job show](/cli/azure/backup/job#az_backup_job_show) om de status van de taak bij te houden.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Afzonderlijke bestanden of mappen herstellen naar een alternatieve locatie

Als u specifieke bestanden of mappen wilt herstellen naar een alternatieve locatie, gebruikt u de cmdlet [az backup restore restore-azurefiles](/cli/azure/backup/restore#az_backup_restore_restore_azurefiles) met de herstelmodus ingesteld op *alternatelocation* en geeft u de volgende doelgerelateerde parameters op:

* **--target-storage-account:** het opslagaccount waarop de back-up van de inhoud wordt hersteld. Het doelopslagaccount moet zich op dezelfde locatie bevinden als de kluis.
* **--target-file-share:** de bestands share binnen het doelopslagaccount waarop de back-up van de inhoud wordt hersteld.
* **--target-folder:** de map onder de bestands share waarop gegevens worden hersteld. Als de inhoud van de back-up moet worden hersteld naar een hoofdmap, geeft u de waarde van de doelmap op als een lege tekenreeks.

In het volgende voorbeeld wordt het *RestoreTest.txt-bestand* dat oorspronkelijk aanwezig is in de bestands share *azurefiles* hersteld naar een alternatieve locatie: de *map restoredata* in de bestands share *azurefiles1* die wordt gehost in het *opslagaccount afaccount1.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Het **kenmerk Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw herstelbewerking. Gebruik de cmdlet az [backup job show](/cli/azure/backup/job#az_backup_job_show) om de status van de taak bij te houden.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Meerdere bestanden of mappen herstellen naar de oorspronkelijke of alternatieve locatie

Als u herstel wilt uitvoeren voor meerdere items, geeft  u de waarde voor de parameter **source-file-path** door als door spatie gescheiden paden van alle bestanden of mappen die u wilt herstellen.

In het volgende voorbeeld worden de *Restore.txt-* en *AFS-tests Report.docx* bestanden op hun oorspronkelijke locatie hersteld.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

De uitvoer ziet er ongeveer als volgt uit:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

Het **kenmerk Naam** in de uitvoer komt overeen met de naam van de taak die is gemaakt door de back-upservice voor uw herstelbewerking. Gebruik de cmdlet az [backup job show](/cli/azure/backup/job#az_backup_job_show) om de status van de taak bij te houden.

Als u meerdere items wilt herstellen naar een alternatieve locatie, gebruikt u de bovenstaande opdracht door doelgerelateerde parameters op te geven, zoals uitgelegd in de sectie Afzonderlijke bestanden of mappen herstellen naar een [alternatieve](#restore-individual-files-or-folders-to-an-alternate-location) locatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [beheren van back-ups van Azure-bestands delen met de Azure CLI.](manage-afs-backup-cli.md)
