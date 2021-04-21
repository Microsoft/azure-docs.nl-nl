---
title: Virtuele harde schijven op een Linux-VM uitbreiden
description: Meer informatie over het uitbreiden van virtuele harde schijven op een Linux-VM met de Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c27b042b78931fd58e43e4bbb06699abe510f385
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762547"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Virtuele harde schijven op een linux-VM uitbreiden met de Azure CLI

In dit artikel wordt beschreven hoe u beheerde schijven voor een virtuele Linux-machine (VM) uitbreidt met de Azure CLI. U kunt [gegevensschijven toevoegen om](add-disk.md) extra opslagruimte te bieden en u kunt ook een bestaande gegevensschijf uitbreiden. De standaardgrootte van de virtuele harde schijf voor het besturingssysteem (OS) is doorgaans 30 GB op een Linux-VM in Azure. 

> [!WARNING]
> Zorg er altijd voor dat uw bestandssysteem in een goede staat is, dat het tabeltype van de schijfpartitie de nieuwe grootte ondersteunt en dat er een back-up van uw gegevens wordt uitgevoerd voordat u de grootte van de schijf kunt bewerken. Zie de snelstart voor Azure Backup [informatie.](../../backup/quick-backup-vm-portal.md) 

## <a name="expand-an-azure-managed-disk"></a>Een Azure Managed Disk uitbreiden
Zorg ervoor dat u de meest recente [Versie van Azure CLI](/cli/azure/install-az-cli2) hebt geïnstalleerd en dat u bent aangemeld bij een Azure-account met behulp van az [login](/cli/azure/reference-index#az_login).

Voor dit artikel is een bestaande VM in Azure vereist, met ten minste één gekoppelde en voorbereide gegevensschijf. Zie Een VM maken en voorbereiden met gegevensschijven als u nog geen VM hebt die u [kunt gebruiken.](tutorial-manage-disks.md#create-and-attach-disks)

Vervang in de volgende voorbeelden voorbeeldparameternamen zoals *myResourceGroup* en *myVM* door uw eigen waarden.

1. Bewerkingen op virtuele harde schijven kunnen niet worden uitgevoerd als de virtuele machine wordt uitgevoerd. Wijs de toewijzing van uw VM op [met az vm deallocate](/cli/azure/vm#az_vm_deallocate). In het volgende voorbeeld wordt de toewijzing van de VM met de *naam myVM* in de resourcegroep *myResourceGroup opheffen:*

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > De toewijzing van de virtuele machine moet worden verbreed om de virtuele harde schijf uit te breiden. Als u de VM stopt met `az vm stop` , worden de rekenbronnen niet vrijgave. Gebruik om rekenbronnen vrij te `az vm deallocate` geven.

1. Bekijk een lijst met beheerde schijven in een resourcegroep met [az disk list](/cli/azure/disk#az_disk_list). In het volgende voorbeeld wordt een lijst met beheerde schijven weergegeven in de resourcegroep met de *naam myResourceGroup:*

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Vouw de vereiste schijf uit met [az disk update](/cli/azure/disk#az_disk_update). In het volgende voorbeeld wordt de beheerde schijf met de *naam myDataDisk uitgebreid* naar *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Wanneer u een beheerde schijf uitbreidt, wordt de bijgewerkte grootte naar boven afgerond op de dichtstbijzijnde grootte van de beheerde schijf. Zie Overzicht van Azure Managed Disks - Prijzen en facturering voor een tabel met de beschikbare beheerde [schijfgrootten en -lagen.](../managed-disks-overview.md)

1. Start uw VM met [az vm start.](/cli/azure/vm#az_vm_start) In het volgende voorbeeld wordt de VM met de *naam myVM* gestart in de resourcegroep met de *naam myResourceGroup:*

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Een schijfpartitie en bestandssysteem uitbreiden
Als u een uitgebreide schijf wilt gebruiken, vouwt u de onderliggende partitie en het bestandssysteem uit.

1. SSH naar uw VM met de juiste referenties. U kunt het openbare IP-adres van uw VM zien met [az vm show](/cli/azure/vm#az_vm_show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --output tsv
    ```

1. Vouw de onderliggende partitie en het bestandssysteem uit.

    a. Als de schijf al is bevestigd, ontkoppelt u deze:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Gebruik `parted` om schijfgegevens weer te geven en de grootte van de partitie aan te geven:

    ```bash
    sudo parted /dev/sdc
    ```

    Bekijk informatie over de bestaande partitie-indeling met `print` . De uitvoer is vergelijkbaar met het volgende voorbeeld, waarin wordt laten zien dat de onderliggende schijf 215 GB is:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Vouw de partitie uit met `resizepart` . Voer het partitienummer, *1* en een grootte in voor de nieuwe partitie:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Voer in om af te `quit` sluiten.

1. Controleer de partitieconsistentie met de grootte van de `e2fsck` partitie:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Het bestandssysteem desize met `resize2fs` :

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. De partitie aan de gewenste locatie, zoals `/datadrive` :

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Gebruik om te controleren of de gegevensschijf is gedimd. `df -h` In de volgende voorbeelduitvoer ziet u dat het gegevensstation */dev/sdc1* nu 200 GB is:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Volgende stappen
* Als u extra opslagruimte nodig hebt, kunt u ook [gegevensschijven toevoegen aan een Linux-VM.](add-disk.md) 
* Zie voor meer informatie over schijfversleuteling [Azure Disk Encryption voor Linux-VM's.](disk-encryption-overview.md)
