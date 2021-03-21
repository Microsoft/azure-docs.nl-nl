---
title: Niet-gekoppelde Azure-schijven identificeren-Azure Portal
description: Het zoeken van niet-gekoppelde, door Azure beheerde en onbeheerde (Vhd's/pagina-blobs)-schijven met behulp van de Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/26/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4d31ce4b6086c44de913afd1083bae25fa3d44cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898152"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>Niet-gekoppelde door Azure beheerde en onbeheerde schijven zoeken en verwijderen-Azure Portal

Wanneer u een virtuele machine (VM) in azure verwijdert, worden schijven die zijn gekoppeld aan de VM standaard niet verwijderd. Dit helpt om gegevens verlies te voor komen vanwege de onbedoelde verwijdering van Vm's. Nadat een virtuele machine is verwijderd, blijft u betalen voor niet-gekoppelde schijven. In dit artikel leest u hoe u niet-gekoppelde schijven zoekt en verwijdert met behulp van de Azure Portal en overbodige kosten kunt verlagen. Verwijderingen zijn permanent, u kunt geen gegevens herstellen wanneer u een schijf verwijdert.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Managed disks: niet-gekoppelde schijven zoeken en verwijderen

Als u niet-gekoppelde beheerde schijven hebt en de gegevens niet meer nodig hebt, wordt in het volgende proces uitgelegd hoe u deze kunt vinden in de Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zoek en selecteer **schijven**.

    Op de Blade **schijven** wordt een lijst met alle schijven weer gegeven. Een schijf met ' **-** ' in de kolom **eigenaar** is een niet-gekoppelde schijf.

    [![Scherm opname van de Blade Managed disks, als een schijf de eigenaar kolom heeft, is het een niet-gekoppelde schijf.](media/disks-find-unattached-portal/managed-disk-unattached-owner.png)](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. Selecteer de niet-gekoppelde schijf die u wilt verwijderen. Hiermee opent u de Blade van de schijf.
1. Op de Blade van de schijf kunt u controleren of de status van de schijf niet is gekoppeld en selecteert u vervolgens **verwijderen**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="Scherm afbeelding van een afzonderlijke Blade Managed disks. In deze Blade ziet u dat de status van de schijf niet-gekoppeld is. U kunt deze schijf verwijderen als u de gegevens niet langer hoeft te bewaren":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Niet-beheerde schijven: niet-gekoppelde schijven zoeken en verwijderen

Onbeheerde schijven zijn VHD-bestanden die zijn opgeslagen als [pagina-blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) in [Azure-opslag accounts](../storage/common/storage-account-overview.md).

Als u niet-beheerde schijven hebt die niet zijn gekoppeld aan een virtuele machine, de gegevens niet meer nodig hebben en u deze wilt verwijderen, wordt in het volgende proces uitgelegd hoe u dit kunt doen vanuit de Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zoek en selecteer **schijven (klassiek)**.

    Er wordt een lijst met al uw onbeheerde schijven weer gegeven. Een schijf met **-** in de kolom **gekoppeld aan** is een niet-gekoppelde schijf.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="Scherm opname van de Blade niet-beheerde schijven. Schijven in deze Blade die de kolom gekoppeld aan hebben, zijn niet gekoppeld.":::

1. Selecteer de niet-gekoppelde schijf die u wilt verwijderen, waarna de Blade van de schijf wordt geopend.

1. Op de Blade van de schijf kunt u bevestigen dat deze niet is gekoppeld, omdat deze nog steeds is **gekoppeld aan** **-** .

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="Scherm afbeelding van een afzonderlijke niet-beheerde schijf. De waarde wordt als gekoppeld aan toegevoegd als deze niet is gekoppeld. Als u deze schijf gegevens niet meer nodig hebt, kunt u deze verwijderen.":::

1. Selecteer **Verwijderen**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="Scherm opname van een afzonderlijke niet-beheerde schijf als markering voor verwijderen.":::

## <a name="next-steps"></a>Volgende stappen

Als u een geautomatiseerde manier wilt om niet-gekoppelde opslag accounts te zoeken en te verwijderen, raadpleegt u onze [cli](linux/find-unattached-disks.md) -of [Power shell](windows/find-unattached-disks.md) -artikelen.

Zie [een opslag account verwijderen](../storage/common/storage-account-create.md#delete-a-storage-account) en [zwevende schijven identificeren met Power shell](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell) voor meer informatie.
