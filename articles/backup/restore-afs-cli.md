---
title: Azure-bestands shares herstellen met Azure CLI
description: Meer informatie over het gebruik van de Azure CLI voor het herstellen van back-ups van Azure-bestands shares in de Recovery Services kluis
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: a025de7bfb9db037b2008d69be7782feabb482f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94562318"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Azure-bestands shares herstellen met Azure CLI

De Azure CLI biedt een opdracht regel ervaring voor het beheer van Azure-resources. Het is een uitstekend hulp programma voor het maken van aangepaste automatisering om Azure-resources te gebruiken. In dit artikel wordt uitgelegd hoe u een volledige bestands share herstelt of specifieke bestanden van een herstel punt dat is gemaakt door [Azure backup](./backup-overview.md) met behulp van de Azure cli. U kunt deze stappen ook uitvoeren met [Azure PowerShell](./backup-azure-afs-automation.md) of in [Azure Portal](backup-afs.md).

Aan het einde van dit artikel leert u hoe u de volgende bewerkingen kunt uitvoeren met de Azure CLI:

* Herstel punten weer geven voor een back-up van een Azure-bestands share.
* Een volledige Azure-bestands share herstellen.
* Afzonderlijke bestanden of mappen herstellen.

>[!NOTE]
> Azure Backup ondersteunt nu het terugzetten van meerdere bestanden of mappen naar de oorspronkelijke of een alternatieve locatie met behulp van Azure CLI. Raadpleeg het gedeelte [meerdere bestanden of mappen terugzetten op de oorspronkelijke of alternatieve locatie](#restore-multiple-files-or-folders-to-original-or-alternate-location) van dit document voor meer informatie.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u al een Azure-bestands share hebt waarvan een back-up is gemaakt door Azure Backup. Als u er nog geen hebt, raadpleegt u back-ups [maken van Azure-bestands shares met de CLI](backup-afs-cli.md) om back-ups te configureren voor uw bestands share. Voor dit artikel gebruikt u de volgende bronnen:

| Bestandsshare | Storage-account | Region | Details |
|---|---|---|---|
| *Azure files* | *afsaccount* | EastUS | De oorspronkelijke bron heeft een back-up gemaakt met behulp van Azure Backup |
| *azurefiles1* | *afaccount1* | EastUS | Doel bron die wordt gebruikt voor herstel naar een alternatieve locatie |

U kunt een vergelijk bare structuur voor uw bestands shares gebruiken om de verschillende typen herstel bewerkingen uit te proberen die in dit artikel worden uitgelegd.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - Voor deze zelfstudie is versie 2.0.18 of hoger Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Herstel punten voor de Azure-bestands share ophalen

Gebruik de cmdlet [AZ backup Recovery Point List](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) om alle herstel punten voor de back-up van de bestands share weer te geven.

In het volgende voor beeld wordt de lijst met herstel punten opgehaald voor de bestands share *Azure files* in het *afsaccount* -opslag account.

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

U kunt ook de vorige cmdlet uitvoeren met behulp van de beschrijvende naam voor de container en het item door de volgende twee extra para meters op te geven:

* **--back-upbeheer-type**: *opslag*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

De resultatenset is een lijst met herstel punten met tijd en consistentie Details voor elk herstel punt.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van het herstel punt dat kan worden gebruikt als waarde voor de para meter **--RP-name** in herstel bewerkingen.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Herstel van volledige shares met behulp van de Azure CLI

U kunt deze terugzet optie gebruiken om de volledige bestands share te herstellen op de oorspronkelijke of een alternatieve locatie.

Definieer de volgende para meters voor het uitvoeren van herstel bewerkingen:

* **--container naam**: de naam van het opslag account dat de back-up van de oorspronkelijke bestands share host. Als u de naam of beschrijvende naam van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--item-name**: de naam van de back-up van de oorspronkelijke bestands share die u wilt gebruiken voor de herstel bewerking. Als u de naam of beschrijvende naam van het back-upitem wilt ophalen, gebruikt u de opdracht [AZ back-upitems](/cli/azure/backup/item#az-backup-item-list) .

### <a name="restore-a-full-share-to-the-original-location"></a>Een volledige share naar de oorspronkelijke locatie herstellen

Wanneer u naar een oorspronkelijke locatie herstelt, hoeft u geen doel-gerelateerde para meters op te geven. Alleen **conflict oplossen** moet worden gegeven.

In het volgende voor beeld wordt de azurefileshare-cmdlet [AZ Backup Restore-](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) de Restore Mode ingesteld op *originallocation* om de *Azure files* -bestands share op de oorspronkelijke locatie te herstellen. U gebruikt het herstel punt 932883129628959823, dat u hebt verkregen bij [het ophalen van herstel punten voor de Azure-bestands share](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die wordt gemaakt door de back-upservice voor uw herstel bewerking. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

### <a name="restore-a-full-share-to-an-alternate-location"></a>Een volledige share naar een alternatieve locatie herstellen

U kunt deze optie gebruiken om een bestands share terug te zetten naar een andere locatie en de oorspronkelijke bestands share te bedenken. Geef de volgende para meters op voor herstel naar een alternatieve locatie:

* **--doel-Storage-account**: het opslag account waarnaar de inhoud van de back-up wordt teruggezet. Het doel-opslag account moet zich op dezelfde locatie berichten als de kluis.
* **--doel bestands share**: de bestands share binnen het doel-opslag account waarnaar de inhoud van de back-up wordt teruggezet.
* **--doelmap**: de map onder de bestands share waarnaar gegevens worden teruggezet. Als de inhoud waarvan een back-up is gemaakt, moet worden hersteld naar een hoofdmap, geeft u de waarden van de doelmap op als een lege teken reeks.
* **--oplossen-conflict**: instructie als er een conflict is met de herstelde gegevens. Accepteert **overschrijven** of **overs Laan**.

In het volgende voor beeld wordt [AZ back Restore Restore-azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) with Restore Mode als *alternatelocation* om de *Azure files* -bestands share in het *afsaccount* -opslag account te herstellen naar de *azurefiles1* -bestands share in het *afaccount1* -opslag account.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die wordt gemaakt door de back-upservice voor uw herstel bewerking. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="item-level-recovery"></a>Herstel op itemniveau

U kunt deze terugzet optie gebruiken om afzonderlijke bestanden of mappen op de oorspronkelijke of een alternatieve locatie te herstellen.

Definieer de volgende para meters voor het uitvoeren van herstel bewerkingen:

* **--container naam**: de naam van het opslag account dat de back-up van de oorspronkelijke bestands share host. Als u de naam of beschrijvende naam van uw container wilt ophalen, gebruikt u de opdracht [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--item-name**: de naam van de back-up van de oorspronkelijke bestands share die u wilt gebruiken voor de herstel bewerking. Als u de naam of beschrijvende naam van het back-upitem wilt ophalen, gebruikt u de opdracht [AZ back-upitems](/cli/azure/backup/item#az-backup-item-list) .

Geef de volgende para meters op voor de items die u wilt herstellen:

* **SourceFilePath**: het absolute pad van het bestand dat in de bestands share moet worden hersteld, als een teken reeks. Dit pad is hetzelfde pad dat wordt gebruikt in het bestand [AZ Storage File down load](/cli/azure/storage/file#az-storage-file-download) of [AZ opslag file show](/cli/azure/storage/file#az-storage-file-show) cli-opdrachten.
* **SourceFileType**: Kies of een map of een bestand is geselecteerd. De **map** of het **bestand** wordt geaccepteerd.
* **ResolveConflict**: instructie als er een conflict is met de herstelde gegevens. Accepteert **overschrijven** of **overs Laan**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Afzonderlijke bestanden of mappen terugzetten op de oorspronkelijke locatie

Gebruik de herstellen [AZ Backup Restore-Azure files](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) cmdlet with Restore Mode set to *originallocation* om specifieke bestanden of mappen op de oorspronkelijke locatie te herstellen.

In het volgende voor beeld wordt het *RestoreTest.txt* -bestand teruggezet op de oorspronkelijke locatie: de bestands share *Azure files* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die wordt gemaakt door de back-upservice voor uw herstel bewerking. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Afzonderlijke bestanden of mappen terugzetten op een andere locatie

Als u specifieke bestanden of mappen naar een andere locatie wilt herstellen, gebruikt u de herstel modus [AZ Backup Restore-Azure files](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) en geeft u de volgende para meters op die zijn gericht op *alternatelocation* :

* **--doel-Storage-account**: het opslag account waarnaar de inhoud van de back-up wordt teruggezet. Het doel-opslag account moet zich op dezelfde locatie berichten als de kluis.
* **--doel bestands share**: de bestands share binnen het doel-opslag account waarnaar de inhoud van de back-up wordt teruggezet.
* **--doelmap**: de map onder de bestands share waarnaar gegevens worden teruggezet. Als de gegevens waarvan een back-up is gemaakt, moeten worden hersteld naar een hoofdmap, geeft u de waarde van de doelmap op als een lege teken reeks.

In het volgende voor beeld wordt het *RestoreTest.txt* bestand dat oorspronkelijk aanwezig is in de *Azure files* -bestands share, teruggezet naar een alternatieve locatie: de map *restoredata* in de *azurefiles1* -bestands share die wordt gehost in het *afaccount1* -opslag account.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die wordt gemaakt door de back-upservice voor uw herstel bewerking. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Meerdere bestanden of mappen terugzetten op de oorspronkelijke of alternatieve locatie

Als u een herstel bewerking voor meerdere items wilt uitvoeren, geeft u de waarde voor de para meter **Source-File-Path** door als **ruimte gescheiden** paden van alle bestanden of mappen die u wilt herstellen.

In het volgende voor beeld worden de *Restore.txt* -en *AFS-Report.docxtest* bestanden op de oorspronkelijke locatie hersteld.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

De uitvoer ziet er ongeveer als volgt uit:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

Het **naam** kenmerk in de uitvoer komt overeen met de naam van de taak die wordt gemaakt door de back-upservice voor uw herstel bewerking. Als u de status van de taak wilt bijhouden, gebruikt u de [opdracht AZ Backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

Als u meerdere items naar een alternatieve locatie wilt herstellen, gebruikt u de bovenstaande opdracht door para meters op basis van het doel op te geven, zoals wordt uitgelegd in het gedeelte [afzonderlijke bestanden of mappen herstellen naar een alternatieve locatie](#restore-individual-files-or-folders-to-an-alternate-location) .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [beheren van back-ups van Azure-bestands shares met de Azure cli](manage-afs-backup-cli.md).
