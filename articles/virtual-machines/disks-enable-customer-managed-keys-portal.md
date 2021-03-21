---
title: Azure Portal-door de klant beheerde sleutels inschakelen met SSE-beheerde schijven
description: Door de klant beheerde sleutels op uw beheerde schijven inschakelen via de Azure Portal.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5452b1e2ffc3ca8a11f3acb591c6288806445e1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499387"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Gebruik de Azure Portal om versleuteling aan de server zijde in te scha kelen met door de klant beheerde sleutels voor beheerde schijven

Met Azure Disk Storage kunt u uw eigen sleutels beheren wanneer u gebruikmaakt van SSE (server side Encryption) voor beheerde schijven, indien gewenst. Zie de sectie door de **klant beheerde sleutels** in ons artikel over schijf versleuteling voor conceptuele informatie over SSE met door de klant beheerde sleutels en andere beheerde schijf versleutelings typen:

- Voor Linux: door de [klant beheerde sleutels](./disk-encryption.md#customer-managed-keys)
- Voor Windows: door de [klant beheerde sleutels](./disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>Beperkingen

De door de klant beheerde sleutels hebben nu de volgende beperkingen:

- Als deze functie is ingeschakeld voor uw schijf, kunt u deze niet uitschakelen.
    Als u dit wilt omzeilen, moet u alle gegevens naar een volledig andere beheerde schijf kopiëren die geen door de klant beheerde sleutels gebruikt:

    - Voor Linux: [een beheerde schijf kopiëren](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Voor Windows: [een beheerde schijf kopiëren](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

- Alleen [software-en HSM RSA-sleutels](../key-vault/keys/about-keys.md) met een grootte van 2.048 bits, 3.072-bits en 4.096 bits worden ondersteund, geen andere sleutels of grootten.
    - Voor [HSM](../key-vault/keys/hsm-protected-keys.md) -sleutels is de **Premium** -laag van Azure-sleutel kluizen vereist.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

De volgende secties bevatten informatie over het inschakelen en gebruiken van door de klant beheerde sleutels voor beheerde schijven:

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Een virtuele machine implementeren

Nu u de sleutel kluis en de schijf versleutelings hebt gemaakt en ingesteld, kunt u een virtuele machine implementeren met behulp van de versleuteling.
Het implementatie proces van de VM is vergelijkbaar met het standaard implementatieproces, maar de enige verschillen zijn dat u de virtuele machine in dezelfde regio als uw andere resources moet implementeren en u ervoor kiest om een door de klant beheerde sleutel te gebruiken.

1. Zoek naar **virtual machines** en selecteer **+ toevoegen** om een virtuele machine te maken.
1. Selecteer op de Blade **basis** dezelfde regio als uw schijf versleutelings en Azure Key Vault.
1. Vul op de Blade **Basic** de andere waarden in zoals u wilt.

    ![Scherm afbeelding van het maken van de VM, waarbij de regio waarde is gemarkeerd.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. Selecteer op  de Blade schijven **versleuteling in rust met een door de klant beheerde sleutel**.
1. Selecteer de schijf versleuteling die u hebt ingesteld in de vervolg keuzelijst **schijf versleuteling instellen** .
1. Breng de resterende selecties naar wens aan.

    ![Scherm afbeelding van de werk ervaring voor het maken van de virtuele machine, de Blade schijven. Met de vervolg keuzelijst schijf versleuteling instellen gemarkeerd.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Inschakelen op een bestaande schijf

> [!CAUTION]
> Als u schijf versleuteling wilt inschakelen op schijven die zijn gekoppeld aan een VM, moet u de virtuele machine stoppen.
    
1. Ga naar een virtuele machine die zich in dezelfde regio bevindt als een van de schijf versleutelings sets.
1. Open de virtuele machine en selecteer **stoppen**.

    ![Scherm afbeelding van de belangrijkste overlay voor uw voorbeeld-VM, met de knop stoppen gemarkeerd.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Nadat de VM is gestopt, selecteert u **schijven** en selecteert u vervolgens de schijf die u wilt versleutelen.

    ![Scherm opname van uw voor beeld-VM, met de Blade schijven geopend. De besturingssysteem schijf is gemarkeerd als een voor beeld van een schijf die u kunt selecteren.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Selecteer **versleuteling** en selecteer **op rest versleuteling met een door de klant beheerde sleutel** en selecteer vervolgens de schijf versleuteling die u hebt ingesteld in de vervolg keuzelijst.
1. Selecteer **Opslaan**.

    ![Scherm opname van de besturingssysteem schijf van uw voor beeld. De versleutelings Blade is geopend. versleuteling in rust met een door de klant beheerde sleutel is geselecteerd, evenals uw voor beeld Azure Key Vault. Nadat u deze selecties hebt gemaakt, is de knop Opslaan geselecteerd.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Herhaal dit proces voor alle andere schijven die zijn gekoppeld aan de virtuele machine die u wilt versleutelen.
1. Als uw schijven zijn overgeschakeld naar door de klant beheerde sleutels en er geen andere gekoppelde schijven zijn die u wilt versleutelen, kunt u de virtuele machine starten.

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw resources onder de kaften. Als u het abonnement, de resource groep of de beheerde schijf vervolgens verplaatst van een Azure AD-Directory naar een andere, wordt de beheerde identiteit die is gekoppeld aan de beheerde schijven, niet overgebracht naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [een abonnement overdragen tussen Azure AD-mappen](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Bekijk de Azure Resource Manager sjablonen voor het maken van versleutelde schijven met door de klant beheerde sleutels](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Wat is Azure Key Vault?](../key-vault/general/overview.md)
- [Computers repliceren met door de klant beheerde sleutels ingeschakelde schijven](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Herstel na noodgevallen van virtuele VMware-machines naar Azure instellen met PowerShell](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Herstel na noodgeval instellen naar Azure voor Hyper-V-VM's met behulp van PowerShell en Azure Resource Manager](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)