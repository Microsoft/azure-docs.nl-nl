---
title: Een beheerde installatie kopie maken in azure
description: Een beheerde installatie kopie maken van een gegeneraliseerde virtuele machine of VHD in Azure. Installatie kopieën kunnen worden gebruikt om meerdere virtuele machines te maken die gebruikmaken van beheerde schijven.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 9b63ec5b8a5d0684a0e144de7dfe4114af9777e2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881886"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Een beheerde installatiekopie maken van een gegeneraliseerde VM in Azure

Een beheerde installatiekopie kan worden gemaakt op basis van een gegeneraliseerde virtuele machine (VM) die als een beheerde schijf of een onbeheerde schijf is opgeslagen in een opslagaccount. De installatiekopie kan vervolgens worden gebruikt om meerdere VM's te maken. Zie [Managed disks-prijzen](https://azure.microsoft.com/pricing/details/managed-disks/)voor meer informatie over hoe beheerde installatie kopieën worden gefactureerd. 

Eén beheerde installatie kopie ondersteunt Maxi maal 20 gelijktijdige implementaties. Als u probeert om meer dan 20 Vm's gelijktijdig te maken, vanuit dezelfde beheerde installatie kopie, kan dit leiden tot het inrichten van time-outs als gevolg van de opslag prestatie beperkingen van één VHD. Als u meer dan 20 Vm's gelijktijdig wilt maken, gebruikt u een afbeelding voor [gedeelde afbeeldings galerieën](../shared-image-galleries.md) die is geconfigureerd met 1 replica voor elke 20 gelijktijdige VM-implementaties.

## <a name="generalize-the-windows-vm-using-sysprep"></a>De Windows VM generaliseren met behulp van Sysprep

Sysprep verwijdert al uw persoonlijke account-en beveiligings gegevens en vervolgens wordt de machine voor bereid voor gebruik als installatie kopie. Zie [overzicht van Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)voor meer informatie over Sysprep.

Zorg ervoor dat de server functies die op de computer worden uitgevoerd, worden ondersteund door Sysprep. Zie [Sysprep-ondersteuning voor Server rollen](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) en [niet-ondersteunde scenario's](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios)voor meer informatie. 

> [!IMPORTANT]
> Nadat u Sysprep op een virtuele machine hebt uitgevoerd, wordt die VM beschouwd als *gegeneraliseerd* en kan niet opnieuw worden gestart. Het generaliseringsproces van een VM is onomkeerbaar. Als u de oorspronkelijke VM goed wilt laten functioneren, moet u een [kopie van de virtuele machine](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) maken en de kopie ervan generaliseren. 
>
>Sysprep vereist dat de stations volledig worden ontsleuteld. Als u versleuteling op uw virtuele machine hebt ingeschakeld, schakelt u versleuteling uit voordat u Sysprep uitvoert.
>
> Als u van plan bent om Sysprep uit te voeren voordat u de virtuele harde schijf (VHD) voor het eerst uploadt naar Azure, moet u ervoor zorgen dat u [uw VM hebt voor bereid](prepare-for-upload-vhd-image.md).  
> 
> 

Voer de volgende stappen uit om uw Windows-VM te generaliseren:

1. Meld u aan bij uw Windows-VM.
   
2. Open een opdracht prompt venster als beheerder. 

3. Verwijder de Panther-map (C:\Windows\Panther). Wijzig vervolgens de map in%windir%\system32\sysprep en voer vervolgens uit `sysprep.exe` .
   
4. In het dialoog venster **hulp programma voor systeem voorbereiding** selecteert u **systeem out-of-Box Experience (OOBE) opgeven** en schakelt u het selectie vakje **generalize** in.
   
5. Selecteer voor **afsluit opties** de optie **Afsluiten**.
   
6. Selecteer **OK**.
   
    ![Sysprep starten](./media/upload-generalized-managed/sysprepgeneral.png)

6. Wanneer Sysprep is voltooid, wordt de VM afgesloten. Start de VM niet opnieuw.

> [!TIP]
> **Optioneel** Gebruik [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) om uw installatie kopie te optimaliseren en de eerste opstart tijd van uw VM te verlagen.
>
> Als u uw installatie kopie wilt optimaliseren, koppelt u uw VHD door erop te dubbel klikken in Windows Verkenner en voert u vervolgens DISM uit met de `/optimize-image` para meter.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Waarbij D: het gekoppelde VHD-pad is.
>
> Uitvoeren `DISM /optimize-image` moet de laatste wijziging zijn die u in uw VHD aanbrengt. Als u wijzigingen aanbrengt in uw VHD vóór de implementatie, moet u het opnieuw uitvoeren `DISM /optimize-image` .

## <a name="create-a-managed-image-in-the-portal"></a>Een beheerde installatie kopie maken in de portal 

1. Ga naar de [Azure Portal](https://portal.azure.com) om de VM-installatie kopie te beheren. Zoek en selecteer **virtuele machines**.

2. Selecteer uw virtuele machine in de lijst.

3. Selecteer op de pagina **virtuele machine** voor de VM de optie **vastleggen** in het bovenste menu.

   De pagina **afbeelding maken** wordt weer gegeven.

4. Voor **naam** accepteert u de vooraf ingevulde naam of voert u een naam in die u wilt gebruiken voor de installatie kopie.

5. Voor **resource groep** selecteert u **Nieuw maken** en voert u een naam in of selecteert u een resource groep die u wilt gebruiken in de vervolg keuzelijst.

6. Als u de bron-VM wilt verwijderen nadat de installatie kopie is gemaakt, selecteert u **deze virtuele machine automatisch verwijderen na het maken van de installatie kopie**.

7. Als u de installatie kopie in elke [beschikbaarheids zone](../../availability-zones/az-overview.md)wilt gebruiken, selecteert u **aan** voor **zone tolerantie**.

8. Selecteer **Maken** om de installatiekopie te maken.

Nadat de installatie kopie is gemaakt, kunt u deze als een **afbeeldings** resource vinden in de lijst met resources in de resource groep.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Een installatie kopie van een virtuele machine maken met behulp van Power shell

 

Als u een installatie kopie rechtstreeks vanuit de VM maakt, zorgt u ervoor dat de installatie kopie alle schijven bevat die zijn gekoppeld aan de virtuele machine, inclusief de besturingssysteem schijf en alle gegevens schijven. In dit voor beeld ziet u hoe u een beheerde installatie kopie maakt op basis van een virtuele machine die gebruikmaakt van beheerde schijven.

Voordat u begint, moet u ervoor zorgen dat u de nieuwste versie van de module Azure PowerShell hebt. Voer in Power shell uit om de versie te vinden `Get-Module -ListAvailable Az` . Als u een upgrade wilt uitvoeren, raadpleegt u [Azure PowerShell op Windows installeren met PowerShellGet](/powershell/azure/install-az-ps). Als u Power shell lokaal uitvoert, voert u uit `Connect-AzAccount` om een verbinding te maken met Azure.


> [!NOTE]
> Als u uw installatie kopie wilt opslaan in zone-redundante opslag, moet u deze maken in een regio die [beschikbaarheids zones](../../availability-zones/az-overview.md) ondersteunt en de `-ZoneResilient` para meter in de installatie kopie configuratie ( `New-AzImageConfig` opdracht) bevatten.

Voer de volgende stappen uit om een VM-installatie kopie te maken:

1. Maak enkele variabelen.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Zorg ervoor dat de toewijzing van de virtuele machine ongedaan is gemaakt.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Stel de status van de virtuele machine in op **gegeneraliseerd**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Haal de virtuele machine op. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Maak de configuratie van installatiekopie.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Maak de installatiekopie.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Een installatie kopie maken van een beheerde schijf met behulp van Power shell

Als u alleen een installatie kopie van de besturingssysteem schijf wilt maken, geeft u de ID van de beheerde schijf op als de besturingssysteem schijf:

    
1. Maak enkele variabelen. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Haal de virtuele machine op.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Haal de ID van de beheerde schijf op.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Maak de configuratie van installatiekopie.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Maak de installatiekopie.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Een installatie kopie maken op basis van een moment opname met behulp van Power shell

U kunt een beheerde installatie kopie maken op basis van een moment opname van een gegeneraliseerde virtuele machine door de volgende stappen uit te voeren:

    
1. Maak enkele variabelen. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. De moment opname ophalen.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Maak de configuratie van installatiekopie.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Maak de installatiekopie.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Een installatie kopie maken van een virtuele machine die gebruikmaakt van een opslag account

Als u een beheerde installatie kopie wilt maken op basis van een virtuele machine die geen beheerde schijven gebruikt, hebt u de URI van de besturingssysteem-VHD in het opslag account nodig, in de volgende indeling: https://*mystorageaccount*. blob.core.Windows.net/*vhdcontainer* / *vhdfilename. VHD*. In dit voor beeld bevindt de VHD zich in *mystorageaccount*, in een container met de naam *VHDCONTAINER* en de VHD-bestands naam *vhdfilename. VHD*.


1.  Maak enkele variabelen.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Stop of hef de toewijzing van de virtuele machine op.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Markeer de virtuele machine als gegeneraliseerd.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Maak de installatie kopie met behulp van de gegeneraliseerde VHD van het besturings systeem.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Volgende stappen
- [Een virtuele machine maken op basis van een beheerde installatie kopie](create-vm-generalized-managed.md). 