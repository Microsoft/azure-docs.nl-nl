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
ms.openlocfilehash: cdb22805e2e68893d3883272b66c2cfac13c807e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721865"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>De Azure Portal gebruiken om end-to-end-versleuteling in te scha kelen met behulp van versleuteling op de host

Wanneer u versleuteling inschakelt op de host, worden gegevens die op de VM-host zijn opgeslagen, versleuteld op rest en stromen die zijn versleuteld naar de opslag service. Zie voor conceptuele informatie over versleuteling op de host en andere beheerde schijf versleutelings typen:

* Linux: [versleuteling bij host-end-to-end-versleuteling voor uw VM-gegevens](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

* Windows: [versleuteling bij host-end-to-end-versleuteling voor uw VM-gegevens](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Vereisten

U moet de functie voor uw abonnement inschakelen voordat u de eigenschap EncryptionAtHost voor uw virtuele machine/VMSS gebruikt. Volg de onderstaande stappen om de functie in te scha kelen voor uw abonnement:

1. **Azure Portal**: selecteer het Cloud shell pictogram op de [Azure Portal](https://portal.azure.com):

    ![Pictogram voor het starten van de Cloud Shell van de Azure Portal](../Cloud-Shell/media/overview/portal-launch-icon.png)
    
2.  Voer de volgende opdracht uit om de functie voor uw abonnement te registreren

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

3.  Controleer of de registratie status is geregistreerd (een paar minuten duurt) met behulp van de onderstaande opdracht voordat u de functie uitprobeert.

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


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

1. Selecteer op  de Blade schijven **Ja** voor **versleuteling op host**.
1. Breng de resterende selecties naar wens aan.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Scherm opname van de Blade schijven voor het maken van virtuele machines, versleuteling op host is gemarkeerd.":::

1. Voltooi het implementatie proces van de VM en maak selecties die passen bij uw omgeving.

U hebt nu een virtuele machine geïmplementeerd met versleuteling op de host ingeschakeld, alle gekoppelde schijven worden versleuteld met versleuteling op de host.

## <a name="next-steps"></a>Volgende stappen

[Azure Resource Manager-voorbeeldsjablonen](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
