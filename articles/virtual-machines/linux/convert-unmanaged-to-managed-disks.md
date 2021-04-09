---
title: Een Linux-VM van niet-beheerde schijven converteren naar beheerde schijven
description: Een Linux-VM van niet-beheerde schijven converteren naar beheerde schijven met behulp van Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1f3b62f8c05edffa1b55bf3d8cd24494b1c918bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558488"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Een virtuele Linux-machine van niet-beheerde schijven converteren naar beheerde schijven

Als u bestaande virtuele Linux-machines (Vm's) hebt die gebruikmaken van niet-beheerde schijven, kunt u de Vm's converteren voor het gebruik van [Azure Managed disks](../managed-disks-overview.md). Dit proces converteert zowel de besturingssysteem schijf als eventuele gekoppelde gegevens schijven.

In dit artikel wordt beschreven hoe u Vm's kunt converteren met behulp van de Azure CLI. Als u deze wilt installeren of upgraden, raadpleegt u [Azure cli installeren](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Voordat u begint
* Raadpleeg [de veelgestelde vragen over migratie naar Managed disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* De oorspronkelijke VHD's en het opslagaccount die vóór de conversie werden gebruikt door de VM worden niet verwijderd. Hiervoor worden nog altijd kosten in rekening gebracht. Als u wilt voorkomen dat er kosten worden doorberekend voor de artefacten, verwijdert u de oorspronkelijke VHD-blobs nadat u hebt gecontroleerd of de conversie is voltooid. Als u deze niet-gekoppelde schijven wilt kunnen verwijderen, raadpleegt u ons artikel niet- [gekoppelde door Azure beheerde en onbeheerde schijven zoeken en verwijderen](find-unattached-disks.md).

## <a name="convert-single-instance-vms"></a>Vm's met één exemplaar converteren
In deze sectie wordt beschreven hoe u virtuele Azure-machines met één exemplaar converteert van niet-beheerde schijven naar beheerde schijven. (Als uw Vm's zich in een beschikbaarheidsset bevinden, raadpleegt u de volgende sectie.) U kunt dit proces gebruiken om de virtuele machines van de niet-beheerde schijven van Premium (SSD) te converteren naar Premium Managed disks of van standaard schijven (HDD) op standaard beheerde schijven.

1. De toewijzing van de virtuele machine ongedaan maken met behulp van [AZ VM deallocate](/cli/azure/vm). In het volgende voor beeld wordt de toewijzing van de virtuele machine met de naam `myVM` in de resource groep met de naam `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Converteer de VM naar Managed disks met behulp van [AZ VM Convert](/cli/azure/vm). Met het volgende proces wordt de VM `myVM` met de naam, met inbegrip van de besturingssysteem schijf en alle gegevens schijven, geconverteerd:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Start de virtuele machine na de conversie naar Managed disks met behulp van [AZ VM start](/cli/azure/vm). In het volgende voor beeld wordt de virtuele machine gestart met de naam `myVM` in de resource groep met de naam `myResourceGroup` .

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Vm's in een beschikbaarheidsset converteren

Als de virtuele machines die u wilt converteren naar Managed disks zich in een beschikbaarheidsset bevinden, moet u eerst de beschikbaarheidsset converteren naar een beheerde beschikbaarheidsset.

U moet de toewijzing van alle virtuele machines in de beschikbaarheidsset ongedaan maken voordat u de beschikbaarheidsset converteert. Plan om alle Vm's te converteren naar Managed disks nadat de beschikbaarheidsset zelf is geconverteerd naar een beheerde beschikbaarheidsset. Vervolgens start u alle Vm's en gaat u als normaal werken.

1. Een lijst met alle virtuele machines in een beschikbaarheidsset met behulp van [AZ VM Availability-set list](/cli/azure/vm/availability-set). In het volgende voor beeld ziet u alle virtuele machines in de beschikbaarheidsset `myAvailabilitySet` met de naam in de resource groep met de naam `myResourceGroup` :

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. De toewijzing van alle Vm's ongedaan maken met behulp van [AZ VM deallocate](/cli/azure/vm). In het volgende voor beeld wordt de toewijzing van de virtuele machine met de naam `myVM` in de resource groep met de naam `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Converteer de beschikbaarheidsset met behulp van [AZ VM Availability-set Convert](/cli/azure/vm/availability-set). In het volgende voor beeld wordt de beschikbaarheidsset geconverteerd met de naam `myAvailabilitySet` in de resource groep met de naam `myResourceGroup` :

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Converteer alle Vm's naar Managed disks met behulp van [AZ VM Convert](/cli/azure/vm). Met het volgende proces wordt de VM `myVM` met de naam, met inbegrip van de besturingssysteem schijf en alle gegevens schijven, geconverteerd:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Start alle Vm's na de conversie naar Managed disks met behulp van [AZ VM start](/cli/azure/vm). In het volgende voor beeld wordt de VM gestart met de naam `myVM` in de resource groep met de naam `myResourceGroup` :

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Converteren met behulp van de Azure Portal

U kunt ook niet-beheerde schijven converteren naar Managed disks met behulp van de Azure Portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer de virtuele machine in de lijst met virtuele machines in de portal.
3. Selecteer in de Blade voor de virtuele machine **schijven** in het menu.
4. Selecteer boven aan de Blade **schijven** **migreren naar Managed disks**.
5. Als uw virtuele machine zich in een beschikbaarheidsset bevindt, wordt er een waarschuwing weer gegeven op de Blade **migreren naar Managed disks** die u eerst moet converteren van de beschikbaarheidsset. De waarschuwing moet een koppeling hebben waarop u kunt klikken om de beschikbaarheidsset te converteren. Zodra de beschikbaarheidsset is geconverteerd of als uw virtuele machine zich niet in een beschikbaarheidsset bevindt, klikt u op **migreren** om het proces van het migreren van uw schijven naar Managed disks te starten.

De virtuele machine wordt gestopt en opnieuw opgestart nadat de migratie is voltooid.

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure Managed disks](../managed-disks-overview.md)voor meer informatie over opslag opties.
