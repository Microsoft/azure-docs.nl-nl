---
title: Virtuele harde schijven op een Linux-VM uitvouwen
description: Meer informatie over het uitbreiden van virtuele harde schijven op een Linux-VM met de Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 72778c431c561f5345dde3d6803e814d6fdebfba
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549121"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Virtuele harde schijven op een Linux VM uitbreiden met de Azure CLI

In dit artikel wordt beschreven hoe u beheerde schijven uitbreidt voor een virtuele Linux-machine (VM) met de Azure CLI. U kunt [gegevens schijven toevoegen](add-disk.md) om te voorzien in extra opslag ruimte, en u kunt ook een bestaande gegevens schijf uitbreiden. De standaard grootte van de virtuele harde schijf voor het besturings systeem (OS) is doorgaans 30 GB op een virtuele Linux-machine in Azure. 

> [!WARNING]
> Zorg er altijd voor dat het bestands systeem in orde is, dat het tabel type van de schijf partitie de nieuwe grootte ondersteunt en controleer of er een back-up van uw gegevens wordt gemaakt voordat u de bewerkingen voor het wijzigen van de schijf uitvoert. Zie [Azure backup Quick](../../backup/quick-backup-vm-portal.md)start voor meer informatie. 

## <a name="expand-an-azure-managed-disk"></a>Een Azure Managed Disk uitbreiden
Zorg ervoor dat u de nieuwste [Azure cli](/cli/azure/install-az-cli2) hebt geïnstalleerd en bent aangemeld bij een Azure-account met behulp van [AZ login](/cli/azure/reference-index#az-login).

Voor dit artikel is een bestaande virtuele machine in azure vereist met ten minste één gekoppelde en voor bereide gegevens schijf. Als u nog geen virtuele machine hebt die u kunt gebruiken, raadpleegt u [een virtuele machine maken en voorbereiden met gegevens schijven](tutorial-manage-disks.md#create-and-attach-disks).

Vervang in de volgende voor beelden voorbeeld parameter namen zoals *myResourceGroup* en *myVM* met uw eigen waarden.

1. Bewerkingen op virtuele harde schijven kunnen niet worden uitgevoerd als de virtuele machine wordt uitgevoerd. Maak de toewijzing van uw VM ongedaan met [AZ VM deallocate](/cli/azure/vm#az-vm-deallocate). In het volgende voor beeld wordt de toewijzing van de virtuele machine met de naam *myVM* in de resource groep met de naam *myResourceGroup* ongedaan gemaakt:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > De toewijzing van de virtuele harde schijf moet worden opgeheven voor de VM. Als de virtuele machine `az vm stop` wordt gestopt, worden de reken resources niet vrijgegeven. Gebruik om reken resources vrij te geven `az vm deallocate` .

1. Bekijk een lijst met beheerde schijven in een resource groep met [AZ Disk List](/cli/azure/disk#az-disk-list). In het volgende voor beeld wordt een lijst met beheerde schijven in de resource groep met de naam *myResourceGroup* weer gegeven:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Vouw de vereiste schijf uit met [AZ Disk Update](/cli/azure/disk#az-disk-update). In het volgende voor beeld wordt de beheerde schijf met de naam *myDataDisk* uitgebreid naar *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Wanneer u een beheerde schijf uitbreidt, wordt de bijgewerkte grootte naar boven afgerond naar de dichtstbijzijnde beheerde schijf grootte. Zie [overzicht van Azure Managed disks-prijzen en facturering](../managed-disks-overview.md)voor een tabel met de beschik bare grootten en-lagen voor beheerde schijven.

1. Start uw VM met [AZ VM start](/cli/azure/vm#az-vm-start). In het volgende voor beeld wordt de virtuele machine met de naam *myVM* in de resource groep met de naam *myResourceGroup* gestart:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Een schijf partitie en bestands systeem uitbreiden
Als u een uitgebreide schijf wilt gebruiken, vouwt u de onderliggende partitie en het bestands systeem uit.

1. SSH naar uw virtuele machine met de juiste referenties. U kunt het open bare IP-adres van uw virtuele machine bekijken met [AZ VM show](/cli/azure/vm#az-vm-show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --output tsv
    ```

1. Vouw de onderliggende partitie en het bestands systeem uit.

    a. Als de schijf al is gekoppeld, ontkoppelt u deze:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Gebruik `parted` om schijf gegevens weer te geven en het formaat van de partitie te wijzigen:

    ```bash
    sudo parted /dev/sdc
    ```

    Informatie over de bestaande partitie-indeling weer geven met `print` . De uitvoer is vergelijkbaar met het volgende voor beeld, waarin wordt weer gegeven dat de onderliggende schijf 215 GB is:

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

    c. Vouw de partitie uit met `resizepart` . Voer het partitie nummer, de *1* en een grootte in voor de nieuwe partitie:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Voer in om af te sluiten `quit` .

1. Controleer met de partitie een andere grootte door de partitie consistentie te controleren met `e2fsck` :

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Formaat van het bestands systeem wijzigen met `resize2fs` :

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Koppel de partitie aan de gewenste locatie, zoals `/datadrive` :

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Gebruik om te controleren of de grootte van de gegevens schijf is gewijzigd `df -h` . In de volgende voorbeeld uitvoer ziet u dat het gegevens station */dev/sdc1* nu 200 GB is:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Volgende stappen
* Als u extra opslag ruimte nodig hebt, kunt u ook [gegevens schijven toevoegen aan een virtuele Linux-machine](add-disk.md). 
* Zie [Azure Disk Encryption voor Linux-vm's](disk-encryption-overview.md)voor meer informatie over schijf versleuteling.
