---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002512"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Regionale beschikbaarheid
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal


1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en navigeer naar de schijf waarnaar u een moment opname wilt maken.
1. Selecteer **een moment opname maken** op de schijf

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Afdruk. De Blade van uw schijf, met * * + moment opname maken * * gemarkeerd, zoals u moet selecteren.":::

1. Selecteer de resource groep die u wilt gebruiken en voer een naam in.
1. Selecteer **stapsgewijs** en selecteer **controleren + maken**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Afdruk. Maak een Blade voor de moment opname, vul de naam in en selecteer stapsgewijs en maak uw moment opname.":::

1. Selecteer **Maken**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Afdruk. Validatie pagina voor uw moment opname, bevestig uw selecties en maak de moment opname.":::

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-Managed disks back-ups kopiëren naar een andere regio met differentiële mogelijkheden van incrementele moment opnamen](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)als u voorbeeld code wilt zien met behulp van de differentiële mogelijkheden van incrementele moment opnamen.
