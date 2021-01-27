---
title: Een virtuele machine maken op basis van een geüploade gegeneraliseerde VHD
description: Upload een gegeneraliseerde VHD naar Azure en gebruik deze om nieuwe virtuele machines te maken in het Resource Manager-implementatie model.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: fd3ace98582a414a82ed4d1007a6c7df4b36d211
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98873092"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Een gegeneraliseerde VHD uploaden en deze gebruiken om nieuwe virtuele machines te maken in Azure

In dit artikel wordt stapsgewijs beschreven hoe u Power shell gebruikt om een VHD van een gegeneraliseerde VM naar Azure te uploaden, een installatie kopie van de VHD te maken en een nieuwe VM te maken op basis van die installatie kopie. U kunt een VHD uploaden die is geëxporteerd uit een on-premises virtualisatie hulpprogramma of vanuit een andere cloud. Het gebruik van [Managed disks](../managed-disks-overview.md) voor de nieuwe virtuele machine vereenvoudigt het beheer van virtuele machines en biedt betere Beschik baarheid wanneer de virtuele machine wordt geplaatst in een beschikbaarheidsset. 

Zie [voorbeeld script voor het uploaden van een VHD naar Azure en het maken van een nieuwe virtuele machine](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)voor een voorbeeld script.

## <a name="before-you-begin"></a>Voordat u begint

- Voordat u een VHD naar Azure uploadt, moet u [een Windows-VHD of VHDX voorbereiden om te uploaden naar Azure](prepare-for-upload-vhd-image.md).
- Controleer [het plan voor de migratie naar Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) voordat u de migratie naar [Managed disks](../managed-disks-overview.md)start.

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>De bron-VM generaliseren met Sysprep

Als u dat nog niet hebt gedaan, moet u Sysprep uitvoeren voor de virtuele machine voordat u de VHD uploadt naar Azure. Sysprep verwijdert onder meer al uw persoonlijke accountinformatie en de machine wordt voorbereid om als een installatiekopie te worden gebruikt. Zie het [overzicht van Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)voor meer informatie over Sysprep.

Zorg ervoor dat de server functies die op de computer worden uitgevoerd, worden ondersteund door Sysprep. Zie [Sysprep-ondersteuning voor Server functies](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)voor meer informatie.

> [!IMPORTANT]
> Als u van plan bent om Sysprep uit te voeren voordat u de VHD voor het eerst uploadt naar Azure, moet u ervoor zorgen dat u [uw VM hebt voor bereid](prepare-for-upload-vhd-image.md). 
> 
> 

1. Meld u aan bij de Windows-VM.
1. Open het venster met de opdrachtprompt als beheerder. 
1. Verwijder de Panther-map (C:\Windows\Panther).
1. Wijzig de Directory in%windir%\system32\sysprep en voer uit `sysprep.exe` .
1. Selecteer in het dialoog venster **hulp programma voor systeem voorbereiding** de optie **systeem out-of-Box Experience (OOBE) opgeven** en zorg ervoor dat het selectie vakje **generalize** is ingeschakeld.
1. Selecteer voor **afsluit opties** de optie **Afsluiten**.
1. Selecteer **OK**.
   
    ![Sysprep starten](./media/upload-generalized-managed/sysprepgeneral.png)
1. Wanneer Sysprep is voltooid, wordt de virtuele machine afgesloten. Start de VM niet opnieuw.


## <a name="upload-the-vhd"></a>De VHD uploaden 

U kunt nu een VHD rechtstreeks uploaden naar een beheerde schijf. Zie [een VHD uploaden naar Azure met Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md)voor instructies.



Zodra de VHD is geüpload naar de beheerde schijf, moet u [Get-AzDisk](/powershell/module/az.compute/get-azdisk) gebruiken om de beheerde schijf op te halen.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>De installatiekopie maken
Maak een beheerde installatie kopie van de gegeneraliseerde door het besturings systeem beheerde schijf. Vervang de volgende waarden door uw eigen gegevens.

Stel eerst enkele variabelen in:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Maak de installatie kopie met behulp van de beheerde schijf.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Maak de installatiekopie.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>De VM maken

Nu u een installatiekopie hebt gemaakt, kunt u een of meer nieuwe VM's van de installatiekopie maken met behulp. In dit voor beeld wordt een VM gemaakt met de naam *myVM* van *MyImage*, in *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Volgende stappen

Meld u aan bij de nieuwe virtuele machine. Zie [verbinding maken en aanmelden bij een virtuele Azure-machine met Windows](connect-logon.md)voor meer informatie.