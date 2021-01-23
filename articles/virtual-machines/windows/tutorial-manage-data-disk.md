---
title: 'Zelfstudie: Azure-schijven beheren met Azure PowerShell'
description: In deze zelfstudie leert u hoe u Azure PowerShell gebruikt voor het maken en beheren van Azure-schijven voor virtuele machines
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8ddc94301ae8954fb0a35827ebd21e090c9dfe89
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734695"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Zelfstudie: Azure-schijven beheren met Azure PowerShell

Virtuele machines in Azure gebruiken schijven voor het opslaan van het besturingssysteem, toepassingen en gegevens van virtuele machines. Bij het maken van een VM is het van belang dat u een schijfgrootte en configuratie kiest die geschikt zijn voor de verwachte werkbelasting. Deze zelfstudie bevat informatie over het implementeren en beheren van VM-schijven. U krijgt informatie over:

> [!div class="checklist"]
> * Besturingssysteemschijven en tijdelijke schijven
> * Gegevensschijven
> * Standard- en Premium-schijven
> * Schijfprestaties
> * Het koppelen en voorbereiden van gegevensschijven

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="default-azure-disks"></a>Standaard Azure-schijven

Wanneer een virtuele Azure-machine wordt gemaakt, worden automatisch twee schijven aan de virtuele machine gekoppeld. 

**Besturingssysteemschijf**: besturingssysteemschijven kunnen tot 4 terabytes groot zijn, en huisvesten het besturingssysteem van de VM's. Als u een nieuwe virtuele machine (VM) maakt van een [Azure Marketplace](https://azure.microsoft.com/marketplace/)-installatiekopie, is die meestal 127 GB (maar sommige installatiekopieën hebben een kleiner formaat besturingssysteemschijf). De besturingssysteemschijf krijgt standaard stationsletter *C:* toegewezen. De schijfcacheconfiguratie van de besturingssysteemschijf is geoptimaliseerd voor besturingssysteemprestaties. De besturingssysteemschijf kan **beter geen** toepassingen of gegevens bevatten. Gebruik voor toepassingen en gegevens een gegevensschijf, zoals verderop in dit artikel wordt beschreven.

**Tijdelijke schijf**: tijdelijke schijven gebruiken een SSD-schijf die zich op dezelfde Azure-host bevindt als de virtuele machine. Tijdelijke schijven leveren zeer goede prestaties en kunnen worden gebruikt voor bewerkingen als tijdelijke gegevensverwerking. Als de virtuele machine wordt verplaatst naar een nieuwe host, worden gegevens die zijn opgeslagen op een tijdelijke schijf echter verwijderd. De grootte van de tijdelijke schijf wordt bepaald door de [VM-grootte](../sizes.md). Tijdelijke schijven krijgen standaard de stationsletter *D:* toegewezen.

## <a name="azure-data-disks"></a>Azure-gegevensschijven

Er kunnen extra gegevensschijven worden toegevoegd voor het installeren van toepassingen en opslaan van gegevens. Gegevensschijven moeten worden gebruikt in situaties waarin duurzame en responsieve gegevensopslag nodig is. De grootte van de virtuele machine bepaalt hoeveel gegevensschijven aan een virtuele machine kunnen worden gekoppeld.

## <a name="vm-disk-types"></a>Typen VM-schijven

Azure biedt twee typen schijven.

**Standard-schijven** - ondersteund door HDD's en bieden voordelige en hoogwaardige opslag. Standard-schijven zijn ideaal voor een kostenefficiënte werkbelasting voor ontwikkelen en testen.

**Premium-schijven** - ondersteund door hoogwaardige schijven met een lage latentie op basis van SSD. Ideaal voor virtuele machines met een productiewerkbelasting. VM-grootten met een  **S** in de [groottenaam](../vm-naming-conventions.md), ondersteunen doorgaans Premium Storage. VM's uit de DS-serie, DSv2-serie, GS-serie en FS-serie ondersteunen premium opslag. Wanneer u een schijfgrootte selecteert, wordt de waarde omhoog afgerond naar het volgende type. Als de schijfgrootte bijvoorbeeld groter dan 64 GB maar kleiner dan 128 GB is, dan is het schijftype P10. 
<br>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Wanneer u een premium opslag-schijf inricht, in tegenstelling tot standaard opslag, bent u verzekerd van de capaciteit, IOPS en doorvoer van die schijf. Als u bijvoorbeeld een P50-schijf maakt, dan richt Azure een opslagcapaciteit van 4.095 GB in, 7.500 IOPS en een doorvoer van 250 MB/s voor die schijf. Uw toepassing kan alle of een deel van de capaciteit en prestaties gebruiken. Premium-SSD-schijven zijn zodanig ontworpen dat er in de voorgaande tabel 99,9% van de tijd minder dan één milliseconde latentie en doel-IOPS en -doorvoer wordt beschreven.

In de bovenstaande tabel wordt het max. IOP's per schijf aangegeven, maar er kan een hoger prestatieniveau worden bereikt door striping van meerdere gegevensschijven. Er kunnen bijvoorbeeld 64 gegevensschijven worden gekoppeld aan Standard_GS5 VM. Als voor elk van deze schijven een P30-grootte wordt gebruikt, kan een maximum van 80.000 IOP's worden behaald. Zie [VM-typen en -grootten](../sizes.md) voor gedetailleerde informatie over het maximum aantal IOP's per VM.

## <a name="create-and-attach-disks"></a>Schijven maken en koppelen

Om het voorbeeld in deze zelfstudie uit te voeren, moet u een bestaande virtuele machine hebben. Maak indien nodig een virtuele machine met de volgende opdrachten.

Stel met [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) de gebruikersnaam en het wachtwoord in die nodig zijn voor de beheerdersaccount op de virtuele machine:


Maak de virtuele machine met [New-AzVM](/powershell/module/az.compute/new-azvm). U wordt gevraagd een gebruikersnaam en wachtwoord in te voeren voor het administrator-account voor de VM.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Maak de aanvankelijke configuratie met [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig). In het volgende voorbeeld wordt een schijf van 128 gigabyte groot geconfigureerd.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Maak de gegevensschijf met de opdracht [New-AzDisk](/powershell/module/az.compute/new-azdisk).

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Haal de virtuele machine waaraan u de gegevensschijf wilt toevoegen op met de opdracht [Get-AzVM](/powershell/module/az.compute/get-azvm).

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Voeg de gegevensschijf toe aan de configuratie van de virtuele machine met de opdracht [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Werk de virtuele machine bij met de opdracht [Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Gegevensschijven voorbereiden

Wanneer een schijf is gekoppeld aan de virtuele machine, moet het besturingssysteem worden geconfigureerd voor gebruik van de schijf. Het volgende voorbeeld laat zien hoe u handmatig de eerste schijf configureert die aan de VM wordt toegevoegd. Dit proces kan ook worden geautomatiseerd met de [Custom Script Extension](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Handmatige configuratie

Maak een RDP-verbinding met de virtuele machine. Open PowerShell en voer dit script uit.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>De gegevensschijf controleren

Bekijk het `StorageProfile` voor de gekoppelde `DataDisks` om te controleren of de gegevensschijf is gekoppeld.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

De uitvoer zou er ongeveer uit moeten zien zoals in dit voorbeeld:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u meer geleerd over onderwerpen over VM-schijven, zoals:

> [!div class="checklist"]
> * Besturingssysteemschijven en tijdelijke schijven
> * Gegevensschijven
> * Standard- en Premium-schijven
> * Schijfprestaties
> * Het koppelen en voorbereiden van gegevensschijven

In de volgende zelfstudie leert u hoe u de configuratie van virtuele machines automatiseert.

> [!div class="nextstepaction"]
> [VM-configuratie automatiseren](./tutorial-automate-vm-deployment.md)
