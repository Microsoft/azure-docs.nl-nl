---
title: Dubbele versleuteling inschakelen op schijven met rest Azure Portal-beheer
description: Schakel dubbele versleuteling in op rest voor uw beheerde schijf gegevens met behulp van de Azure Portal.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b5f58b549fc9d879f6c2e5515c679d82c3ff44e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560358"
---
# <a name="use-the-azure-portal-to-enable-double-encryption-at-rest-for-managed-disks"></a>Gebruik de Azure Portal om dubbele versleuteling in te scha kelen voor beheerde schijven

Azure Disk Storage ondersteunt dubbele versleuteling in rust voor beheerde schijven. Zie de sectie [dubbele versleuteling bij rest](disk-encryption.md#double-encryption-at-rest) van ons artikel over schijf versleuteling voor conceptuele informatie over dubbele versleuteling in rust, evenals andere beheerde schijf versleutelings typen.

## <a name="getting-started"></a>Aan de slag

1. Meld u aan bij [Azure Portal](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > U moet de [beschik bare koppeling](https://aka.ms/diskencryptionupdates) gebruiken om toegang te krijgen tot de Azure Portal. Dubbele versleuteling bij rest is momenteel niet zichtbaar in de open bare Azure Portal zonder de koppeling te gebruiken.

1. Zoek en selecteer **schijf versleutelings sets**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Scherm opname van de belangrijkste Azure Portal, schijf versleutelings sets worden gemarkeerd op de zoek balk.":::

1. Selecteer **+ Toevoegen**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Scherm afbeelding van de Blade schijf versleuteling instellen, + toevoegen is gemarkeerd.":::

1. Selecteer een van de ondersteunde regio's.
1. Voor **versleutelings type** selecteert u **dubbele versleuteling met door het platform beheerde en door de klant beheerde sleutels**.

    > [!NOTE]
    > Wanneer u een schijf versleutelings met een bepaald versleutelings type hebt gemaakt, kan deze niet meer worden gewijzigd. Als u een ander versleutelings type wilt gebruiken, moet u een nieuwe schijf versleutelings maken.

1. Vul de resterende gegevens in.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="Scherm opname van de Blade voor het maken van schijf versleuteling, regio's en dubbele versleuteling met door het platform beheerde en door de klant beheerde sleutels worden gemarkeerd.":::

1. Selecteer een Azure Key Vault en een sleutel of maak indien nodig een nieuwe.

    > [!NOTE]
    > Als u een Key Vault exemplaar maakt, moet u de beveiliging voor voorlopig verwijderen en leegmaken inschakelen. Deze instellingen zijn verplicht wanneer u een Key Vault gebruikt voor het versleutelen van beheerde schijven, en u kunt voor komen dat gegevens verloren gaan als gevolg van onbedoeld verwijderen.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Scherm opname van de Blade Key Vault maken.":::

1. Selecteer **Maken**.
1. Navigeer naar de schijf Encryption set die u hebt gemaakt en selecteer de fout die wordt weer gegeven. Hiermee configureert u de schijf versleuteling zo dat deze is ingesteld op werk.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Scherm opname van de weer gegeven schijf versleuteling fout, de fout tekst is: als u een schijf, installatie kopie of moment opname aan deze schijf versleutelings wilt koppelen, moet u machtigingen verlenen aan de sleutel kluis.":::

    Er wordt een melding over de pop-up en geslaagd. Als u dit doet, kunt u de schijf versleuteling instellen met uw sleutel kluis.
    
    ![Scherm opname van geslaagde machtiging en roltoewijzing voor uw sleutel kluis.](media/virtual-machines-disks-double-encryption-at-rest-portal/disk-encryption-notification-success.png)

1. Navigeer naar uw schijf.
1. Selecteer **Versleuteling**.
1. Voor **versleutelings type** selecteert u **dubbele versleuteling met door het platform beheerde en door de klant beheerde sleutels**.
1. Selecteer uw schijf versleutelings.
1. selecteer **Opslaan**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Scherm afbeelding van de Blade versleuteling voor uw beheerde schijf, wordt het eerder versleutelings type gemarkeerd.":::

U hebt nu dubbele versleuteling ingeschakeld bij de rest van uw beheerde schijf.


## <a name="next-steps"></a>Volgende stappen

- [Azure PowerShell-door de klant beheerde sleutels inschakelen met versleuteling aan server zijde-beheerde schijven](./windows/disks-enable-customer-managed-keys-powershell.md)
- [Azure Resource Manager-voorbeeldsjablonen](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Door de klant beheerde sleutels met versleuteling aan server zijde inschakelen-voor beelden](./linux/disks-enable-customer-managed-keys-cli.md#examples)