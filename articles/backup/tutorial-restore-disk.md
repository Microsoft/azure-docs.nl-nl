---
title: 'Zelfstudie: een VM herstellen met Azure CLI'
description: Leer hoe u een schijf kunt herstellen en een herstel-VM maken in Azure met Backup and Recovery Services.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 45e171e064cbd8be5418e20784e6034830d27fe9
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566670"
---
# <a name="restore-a-vm-with-azure-cli"></a>Een VM herstellen met Azure CLI

Azure Backup maakt herstelpunten die worden opgeslagen in geografisch redundante Recovery Services-kluizen. Wanneer u vanaf een herstelpunt herstelt, kunt u de hele VM of afzonderlijke bestanden herstellen. In dit artikel wordt uitgelegd hoe u een volledige VM herstelt met behulp van CLI. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Herstelpunten in een lijst opnemen en selecteren
> * Een schijf herstellen vanaf een herstelpunt
> * Een VM maken op basis van de herstelde schijf

Zie [Back up and restore Azure VMs with PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm) (Back-ups maken en Azure-VM’s herstellen met PowerShell) voor meer informatie over het gebruik van PowerShell om een schijf te herstellen en een herstelde VM te maken.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0.18 of hoger Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

 - Deze zelfstudie vereist een Linux-VM die met Azure Backup is beschermd. Om het per ongeluk verwijderen van een VM en het herstelproces te simuleren, maakt u een VM op basis van een schijf in een herstelpunt. Zie [Een back-up van een virtuele machine maken in Azure met de CLI](quick-backup-vm-cli.md) als u een Linux-VM nodig hebt die is beschermd met Azure Backup.

## <a name="backup-overview"></a>Overzicht van Backup

Wanneer Azure een back-up begint, maakt de back-upextensie op de VM een momentopname van een bepaald tijdstip. De back-upextensie wordt geïnstalleerd op de VM wanneer de eerste back-up wordt aangevraagd. Azure Backup kan ook een momentopname van de onderliggende opslag maken als de VM niet wordt uitgevoerd ten tijde van de back-up.

Standaard maakt Azure Backup een back-up die consistent is met een bestandssysteem. Nadat Azure Backup de momentopname heeft gemaakt, worden de gegevens overgedragen naar de Recovery Services-kluis. Voor maximale efficiëntie identificeert Azure Backup welke gegevensblokken sinds de vorige back-up zijn gewijzigd. Alleen deze worden vervolgens overgedragen.

Wanneer de gegevensoverdracht is voltooid, wordt de momentopname verwijderd en wordt er een herstelpunt gemaakt.

## <a name="list-available-recovery-points"></a>Lijst met beschikbare herstelpunten maken

Voor het herstellen van een schijf selecteert u een herstelpunt als bron voor de te herstellen gegevens. Aangezien het standaardbeleid elke dag een herstelpunt maakt en gedurende 30 dagen bewaart, kunt u een reeks herstelpunten behouden waarmee u een bepaald tijdstip kunt kiezen om te herstellen.

Gebruik [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) om een lijst met beschikbare herstelpunten te zien. De **naam** van het herstelpunt wordt gebruikt om schijven te herstellen. In deze zelfstudie willen we het meest recente beschikbare herstelpunt gebruiken. Met de parameter `--query [0].name` selecteert u als volgt de naam van het meest recente herstelpunt:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Een VM-schijf herstellen

> [!IMPORTANT]
> Het wordt sterk aanbevolen om Az CLI-versie 2.0.74 of hoger te gebruiken om alle voordelen van een snel herstel te kunnen gebruiken, waaronder het herstellen van beheerde schijven. Het is het beste als u altijd de meest recente versie gebruikt.

### <a name="managed-disk-restore"></a>Herstellen van beheerde schijven

Als de VM waarvan een back-up is gemaakt, beheerde schijven bevat, en als het de bedoeling is dat beheerde schijven worden hersteld vanaf het herstelpunt, geeft u eerst een Azure-opslagaccount op. Dit opslagaccount wordt gebruikt om de VM-configuratie en de implementatiesjabloon op te slaan die later kunnen worden gebruikt om de VM te implementeren vanaf de herstelde schijven. Vervolgens geeft u ook een doelresourcegroep op waarin de beheerde schijven moeten worden hersteld.

1. Gebruik [az storage account create](/cli/azure/storage/account#az-storage-account-create) om een opslagaccount te maken. De naam van het opslagaccount mag alleen kleine letters bevatten, en moet globaal uniek zijn. Vervang *mystorageaccount* door uw eigen unieke naam:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Herstel de schijf vanaf uw herstelpunt met [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks). Vervang *mystorageaccount* door de naam van het opslagaccount dat u met de vorige opdracht hebt gemaakt. Vervang *myRecoveryPointName* door de naam van het herstelpunt dat u hebt verkregen met de uitvoer van de vorige opdracht [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list). ***Geef ook de doelresourcegroep op waarin de beheerde schijven worden hersteld** _.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --target-resource-group targetRG
    ```

    > [!WARNING]
    > Als _ *target-resource-group** niet is opgegeven, worden de beheerde schijven als niet-beheerde schijven hersteld in het opgegeven opslagaccount. Dit heeft aanzienlijke gevolgen voor de hersteltijd, omdat de tijd die nodig is om de schijven te herstellen, alleen afhankelijk is van het opgegeven opslagaccount. U kunt alleen profiteren van direct herstellen wanneer de parameter target-resource-group is opgegeven. Als het de bedoeling is dat beheerde schijven worden hersteld als niet-beheerd, geeft u de parameter **target-resource-group** niet op, en geeft u in plaats hiervan de parameter **restore-as-unmanaged-disk** op, zoals hieronder wordt weergegeven. Deze parameter is beschikbaar vanaf Az-versie 3.4.0.

    ```azurecli-interactive
    az backup restore restore-disks \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --storage-account mystorageaccount \
    --rp-name myRecoveryPointName \
    --restore-as-unmanaged-disk
    ```

Hiermee worden beheerde schijven als niet-beheerde schijven hersteld in het opgegeven opslagaccount, en wordt niet gebruikgemaakt van de functie voor direct herstel. In toekomstige versies van CLI moet u verplicht de parameter **target-resource-group** of de parameter **restore-as-unmanaged-disk** opgeven.

### <a name="unmanaged-disks-restore"></a>Niet-beheerde schijven herstellen

Als de VM waarvan een back-up is gemaakt, niet-beheerde schijven bevat, en als het de bedoeling is dat schijven worden hersteld vanaf het herstelpunt, geeft u eerst een Azure-opslagaccount op. Dit opslagaccount wordt gebruikt om de VM-configuratie en de implementatiesjabloon op te slaan die later kunnen worden gebruikt om de VM te implementeren vanaf de herstelde schijven. Standaard worden de niet-beheerde schijven hersteld in de oorspronkelijke opslagaccounts. Als u alle niet-beheerde schijven wilt herstellen op één locatie, kan het opgegeven opslagaccount ook worden gebruikt als faseringslocatie voor deze schijven.

In latere stappen wordt de herstelde schijf gebruikt voor het maken van een VM.

1. Gebruik [az storage account create](/cli/azure/storage/account#az-storage-account-create) om een opslagaccount te maken. De naam van het opslagaccount mag alleen kleine letters bevatten, en moet globaal uniek zijn. Vervang *mystorageaccount* door uw eigen unieke naam:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Herstel de schijf vanaf uw herstelpunt met [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks). Vervang *mystorageaccount* door de naam van het opslagaccount dat u met de vorige opdracht hebt gemaakt. Vervang *myRecoveryPointName* door de naam van het herstelpunt dat u hebt verkregen in de uitvoer van de vorige opdracht [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list):

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Zoals hierboven is vermeld, worden de niet-beheerde schijven hersteld in het oorspronkelijke opslagaccount. Dit zorgt voor de beste prestaties bij het herstellen. Maar als alle niet-beheerde schijven moeten worden hersteld naar een opgegeven opslagaccount, gebruikt u de relevante vlag, zoals hieronder wordt weergegeven.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](/cli/azure/backup/job#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

De uitvoer is vergelijkbaar met het volgende voorbeeld, waarin u kunt zien dat de hersteltaak wordt uitgevoerd (*InProgress*):

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Wanneer de *Status* van de hersteltaak *Voltooid* is, is de benodigde informatie (VM-configuratie en de implementatiesjabloon) hersteld in het opslagaccount.

## <a name="create-a-vm-from-the-restored-disk"></a>Een VM maken op basis van de herstelde schijf

De laatste stap is het maken van een VM vanaf de herstelde schijven. U kunt de implementatiesjabloon die is gedownload naar het opgegeven opslagaccount, gebruiken om de VM te maken.

### <a name="fetch-the-job-details"></a>De taakdetails ophalen

De resulterende taakdetails bevatten de sjabloon-URI die kan worden opgevraagd en geïmplementeerd. Gebruik de opdracht voor het weergeven van de taak om meer details te zien voor de geactiveerde herstelde taak.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

De uitvoer van deze query geeft alle details, maar we zijn alleen geïnteresseerd in de inhoud van het opslagaccount. We kunnen de [querymogelijkheden](/cli/azure/query-azure-cli) van Azure CLI gebruiken om de relevante gegevens op te halen

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>De implementatiesjabloon ophalen

De sjabloon is niet direct toegankelijk omdat deze zich onder het opslagaccount van de klant en de opgegeven container bevindt. We hebben de volledige URL nodig (samen met een tijdelijk SAS-token) om toegang te krijgen tot deze sjabloon.

Extraheer eerst de sjabloonblob-URI uit de taakdetails

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

De sjabloonblob-URI heeft deze indeling, en de sjabloonnaam wordt geëxtraheerd

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

De sjabloonnaam in het bovenstaande voorbeeld wordt dus ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json```, en de naam van de container is ```myVM-daa1931199fd4a22ae601f46d8812276```

Haal nu het SAS-token voor deze container en sjabloon op, zoals [hier](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-cli#provide-sas-token-during-deployment) wordt beschreven

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>De sjabloon implementeren om de VM te maken

Implementeer nu de sjabloon om de VM te maken, zoals [hier](../azure-resource-manager/templates/deploy-cli.md) wordt uitgelegd.

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Om te bevestigen dat uw VM van uw herstelde schijf is gemaakt, maakt u als volgt een lijst van de VM's in uw resourcegroep met [az vm list](/cli/azure/vm#az-vm-list):

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een schijf van een herstelpunt hersteld en vervolgens een VM van de schijf gemaakt. U hebt geleerd hoe u:

> [!div class="checklist"]
>
> * Herstelpunten in een lijst opnemen en selecteren
> * Een schijf herstellen vanaf een herstelpunt
> * Een VM maken op basis van de herstelde schijf

Ga naar de volgende zelfstudie voor meer informatie over het herstellen van afzonderlijke bestanden van een herstelpunt.

> [!div class="nextstepaction"]
> [Bestanden herstellen naar een virtuele machine in Azure](tutorial-restore-files.md)
