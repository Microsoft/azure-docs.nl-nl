---
title: End-to-end-versleuteling inschakelen met behulp van versleuteling op Azure Portal-beheerde schijven
description: Gebruik versleuteling op de host om end-to-end versleuteling in te scha kelen op uw Azure Managed disks-Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: fd74872bc1c46f3dddda1b6d15f14f26dc2187cb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499370"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>De Azure Portal gebruiken om end-to-end-versleuteling in te scha kelen met behulp van versleuteling op de host

Wanneer u versleuteling inschakelt op de host, worden gegevens die op de VM-host zijn opgeslagen, versleuteld op rest en stromen die zijn versleuteld naar de opslag service. Zie voor conceptuele informatie over versleuteling op de host en andere beheerde schijf versleutelings typen:

* Linux: [versleuteling bij host-end-to-end-versleuteling voor uw VM-gegevens](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

* Windows: [versleuteling bij host-end-to-end-versleuteling voor uw VM-gegevens](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Ondersteunde regio’s

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Vereisten

Als u versleuteling wilt gebruiken op de host voor uw Vm's of virtuele-machine schaal sets, moet u de functie inschakelen voor uw abonnement. Stuur een e-mail naar encryptionAtHost@microsoft. com met uw abonnement-Id's om de functie in te scha kelen voor uw abonnementen.

Meld u aan bij de Azure Portal met behulp [van de beschik bare koppeling](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> U moet de [beschik bare koppeling](https://aka.ms/diskencryptionupdates) gebruiken om toegang te krijgen tot de Azure Portal. Versleuteling op host is momenteel niet zichtbaar in de open bare Azure Portal zonder de koppeling te gebruiken.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Een Azure Key Vault-en schijf versleutelings maken

Zodra de functie is ingeschakeld, moet u een Azure Key Vault en een schijf versleutelings instellen, als u dat nog niet hebt gedaan.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Een virtuele machine implementeren

U moet een nieuwe VM implementeren om versleuteling op de host in te scha kelen en deze kan niet worden ingeschakeld op bestaande Vm's.

1. Zoek naar **virtual machines** en selecteer **+ toevoegen** om een virtuele machine te maken.
1. Maak een nieuwe virtuele machine, selecteer een geschikte regio en een ondersteunde VM-grootte.
1. Vul de andere waarden op de Blade **basis** naar wens in en ga vervolgens door naar de Blade **schijven** .

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Scherm opname van de Blade basis beginselen voor het maken van virtuele machines, de regio en de grootte van V M zijn gemarkeerd.":::

1. Selecteer op **Disks** de Blade schijven **Ja** voor **versleuteling op host**.
1. Breng de resterende selecties naar wens aan.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Scherm opname van de Blade schijven voor het maken van virtuele machines, versleuteling op host is gemarkeerd.":::

1. Voltooi het implementatie proces van de VM en maak selecties die passen bij uw omgeving.

U hebt nu een virtuele machine geïmplementeerd met versleuteling op de host ingeschakeld, alle gekoppelde schijven worden versleuteld met versleuteling op de host.

## <a name="next-steps"></a>Volgende stappen

[Azure Resource Manager-voorbeeldsjablonen](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)