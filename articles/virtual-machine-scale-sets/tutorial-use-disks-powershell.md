---
title: Zelfstudie - Schijven voor schaalsets maken en gebruiken met Azure PowerShell
description: Leer hoe u met Azure PowerShell beheerde schijven kunt maken en gebruiken met schaalsets met virtuele machines, waaronder het toevoegen, voorbereiden, opvragen en loskoppelen van schijven.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 9e995e88b80bf14f9c7784f465bcd3d89d0bed65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92367955"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-azure-powershell"></a>Zelfstudie: Schijven met virtuele-machineschaalset maken en gebruiken met Azure PowerShell

Schaalsets voor virtuele machines maken gebruik van schijven voor het opslaan van het besturingssysteem, toepassingen en gegevens van het VM-exemplaar. Bij het maken en beheren van een schaalset is het belangrijk dat u een schijfgrootte en configuratie kiest die geschikt zijn voor de verwachte werkbelasting. Deze zelfstudie bevat informatie over het maken en beheren van VM-schijven. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Besturingssysteemschijven en tijdelijke schijven
> * Gegevensschijven
> * Standard- en Premium-schijven
> * Schijfprestaties
> * Gegevensschijven koppelen en voorbereiden

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="default-azure-disks"></a>Standaard Azure-schijven
Op het moment dat u een schaalset maakt, worden er automatisch twee schijven aan elk VM-exemplaar gekoppeld. 

**Besturingssysteemschijf**: besturingssysteemschijven kunnen tot 2 TB groot zijn, en hosten het besturingssysteem van het VM-exemplaar. De besturingssysteemschijf is standaard gelabeld met */dev/sda*. De schijfcacheconfiguratie van de besturingssysteemschijf is geoptimaliseerd voor besturingssysteemprestaties. Vanwege deze configuratie kan de besturingssysteemschijf **beter geen** toepassingen of gegevens hosten. Gebruik voor toepassingen en gegevens gegevensschijven, die verderop in dit artikel worden beschreven. 

**Tijdelijke schijf**: tijdelijke schijven gebruiken een SSD-schijf die zich op dezelfde Azure-host bevindt als het VM-exemplaar. Deze schijven leveren zeer goede prestaties en kunnen worden gebruikt voor bewerkingen zoals tijdelijke gegevensverwerking. Als het VM-exemplaar echter wordt verplaatst naar een nieuwe host, worden gegevens die zijn opgeslagen op een tijdelijke schijf verwijderd. De grootte van de tijdelijke schijf wordt bepaald door de grootte van het VM-exemplaar. Tijdelijke schijven zijn gelabeld als */dev/sdb* en hebben een koppelpunt van */mnt*.

### <a name="temporary-disk-sizes"></a>Groottes van tijdelijke schijven
| Type | Veelgebruikte grootten | Maximumgrootte van tijdelijke schijf (GiB) |
|----|----|----|
| [Algemeen doel](../virtual-machines/sizes-general.md) | A-, B- en D-serie | 1600 |
| [Geoptimaliseerde rekenkracht](../virtual-machines/sizes-compute.md) | F-serie | 576 |
| [Geoptimaliseerd geheugen](../virtual-machines/sizes-memory.md) | D-, E-, G- en M-serie | 6144 |
| [Geoptimaliseerde opslag](../virtual-machines/sizes-storage.md) | L-serie | 5630 |
| [GPU](../virtual-machines/sizes-gpu.md) | N-serie | 1440 |
| [Hoge prestaties](../virtual-machines/sizes-hpc.md) | A- en H-serie | 2000 |


## <a name="azure-data-disks"></a>Azure-gegevensschijven
Er kunnen extra gegevensschijven worden toegevoegd voor het installeren van toepassingen en het opslaan van gegevens. Gegevensschijven moeten worden gebruikt in situaties waarin duurzame en responsieve gegevensopslag gewenst is. Elke gegevensschijf heeft een maximale capaciteit van 4 TB. De grootte van het VM-exemplaar bepaalt hoeveel gegevensschijven er kunnen worden gekoppeld. Voor elke VM-vCPU kunnen twee schijven worden gekoppeld.

### <a name="max-data-disks-per-vm"></a>Max. aantal gegevensschijven per VM
| Type | Veelgebruikte grootten | Max. aantal gegevensschijven per VM |
|----|----|----|
| [Algemeen doel](../virtual-machines/sizes-general.md) | A-, B- en D-serie | 64 |
| [Geoptimaliseerde rekenkracht](../virtual-machines/sizes-compute.md) | F-serie | 64 |
| [Geoptimaliseerd geheugen](../virtual-machines/sizes-memory.md) | D-, E-, G- en M-serie | 64 |
| [Geoptimaliseerde opslag](../virtual-machines/sizes-storage.md) | L-serie | 64 |
| [GPU](../virtual-machines/sizes-gpu.md) | N-serie | 64 |
| [Hoge prestaties](../virtual-machines/sizes-hpc.md) | A- en H-serie | 64 |


## <a name="vm-disk-types"></a>Typen VM-schijven
Azure biedt twee typen schijven.

### <a name="standard-disk"></a>Standard-schijf
Standard Storage wordt ondersteund door HDD's en biedt voordelige en hoogwaardige opslag. Standard-schijven zijn ideaal voor een kostenefficiënte werkbelasting voor ontwikkelen en testen.

### <a name="premium-disk"></a>Premium-schijf
Premium-schijven worden ondersteund door hoogwaardige SSD-schijven met een lage latentie. Deze schijven worden aanbevolen voor VM's waarop productieworkloads worden uitgevoerd. Premium Storage ondersteunt virtuele machines uit de DS-serie, DSv2-serie GS-serie en FS-serie. Wanneer u een schijfgrootte selecteert, wordt de waarde omhoog afgerond naar het volgende type. Als de schijfgrootte bijvoorbeeld kleiner dan 128 GB is, is het schijftype P10. Als de schijfgrootte tussen 129 en 512 GB is, is het type een P20. Alles groter dan 512 GB is een P30.

### <a name="premium-disk-performance"></a>Prestaties Premium-schijf
|Schijftype voor Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Schijfgrootte (afronden) | 32 GB | 64 GB | 128 GB | 512 GB | 1\.024 GB (1 TB) | 2\.048 GB (2 TB) | 4\.095 GB (4 TB) |
| Max. aantal IOP's per schijf | 120 | 240 | 500 | 2\.300 | 5\.000 | 7\.500 | 7\.500 |
Doorvoer per schijf | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

In de bovenstaande tabel wordt het max. IOP's per schijf aangegeven, maar er kan een hoger prestatieniveau worden bereikt door striping van meerdere gegevensschijven. Een virtuele machine van het type Standard_GS5 kan bijvoorbeeld maximaal 80.000 IOPS bereiken. Zie [Windows VM-grootten](../virtual-machines/sizes.md) voor gedetailleerde informatie over het maximum aantal IOP's per VM.


## <a name="create-and-attach-disks"></a>Schijven maken en koppelen
U kunt schijven maken en koppelen wanneer u een schaalset maakt, maar ook voor een bestaande schaalset.

Vanaf API-versie `2019-07-01` kunt u de grootte van de besturingssysteemschijf instellen in een schaalset voor virtuele machines met de eigenschap [storageProfile.osDisk.diskSizeGb](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk). Na het inrichten moet u de schijf wellicht uitbreiden of partitioneren om gebruik te maken van de volledige ruimte. Meer informatie over [de schijf uitbreiden vindt u hier](../virtual-machines/windows/expand-os-disk.md#expand-the-volume-within-the-os).

### <a name="attach-disks-at-scale-set-creation"></a>Schijven koppelen bij het maken van een schaalset
Maak een virtuele-machineschaalset met behulp van [New-AzVmss](/powershell/module/az.compute/new-azvmss). Geef desgevraagd een gebruikersnaam en wachtwoord op voor de VM-exemplaren. Om het verkeer te distribueren naar de verschillende VM-exemplaren, wordt er ook een load balancer gemaakt. De load balancer bevat regels voor het distribueren van verkeer op TCP-poort 80, en voor het toestaan van verkeer van Extern bureaublad op TCP-poort 3389 en externe toegang via PowerShell op TCP-poort 5985.

Er worden twee schijven gemaakt met de parameter `-DataDiskSizeGb`. De eerste schijf is *64* GB en de tweede schijf *128* GB. Geef desgevraagd uw eigen beheerdersreferenties op voor de VM-exemplaren in de schaalset:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -DataDiskSizeInGb 64,128
```

Het duurt enkele minuten om alle schaalsetresources en VM-exemplaren te maken en te configureren.

### <a name="attach-a-disk-to-existing-scale-set"></a>Een schijf koppelen aan een bestaande schaalset
U kunt ook schijven koppelen aan een bestaande schaalset. Gebruik de schaalset die in de vorige stap is gemaakt om een andere schijf toe te voegen met [Add-AzVmssDataDisk](/powershell/module/az.compute/add-azvmssdatadisk). In het volgende voorbeeld wordt een extra schijf van *128* GB gekoppeld aan een bestaande schaalset:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Attach a 128 GB data disk to LUN 2
Add-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -CreateOption Empty `
  -Lun 2 `
  -DiskSizeGB 128

# Update the scale set to apply the change
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="prepare-the-data-disks"></a>De gegevensschijven voorbereiden
De schijven die worden gemaakt en die worden gekoppeld aan de VM-exemplaren in uw schaalset zijn RAW-schijven. U moet dit type schijven voorbereiden voordat u ze kunt gebruiken met uw gegevens en toepassingen. Dit doet u door een partitie en een bestandssysteem te maken en de schijven vervolgens te koppelen.

Als u dit proces wilt automatiseren voor meerdere VM-exemplaren in een schaalset, kunt u de aangepaste scriptextensie van Azure gebruiken. Met deze extensie kunt u scripts lokaal uitvoeren op elk VM-exemplaar, bijvoorbeeld om gekoppelde gegevensschijven voor te bereiden. Zie voor meer informatie het [overzicht van de aangepaste scriptextensie](../virtual-machines/extensions/custom-script-windows.md).


In het volgende voorbeeld wordt met [Add-AzVmssExtension](/powershell/module/az.compute/Add-AzVmssExtension) op elk VM-exemplaar een voorbeeldscript uit een GitHub-repository uitgevoerd waarmee alle gekoppelde RAW-gegevensschijven worden voorbereid:


```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Define the script for your Custom Script Extension to run
$publicSettings = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
}

# Use Custom Script Extension to prepare the attached data disks
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Als u wilt controleren of de schijven goed zijn voorbereid, gaat u met RDP naar een van de VM-exemplaren. 

Haal eerst het load balancer-object op met [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer). Bekijk vervolgens de inkomende NAT-regels met [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig). De NAT-regels vermelden de *FrontendPort* voor elk VM-exemplaar waarop RDP luistert. Vraag ten slotte het openbare IP-adres van de load balancer op met [Get-AzPublicIPAddress](/powershell/module/az.network/Get-AzPublicIpAddress):


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort

# View the public IP address of the load balancer
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIPAddress | Select IpAddress
```

U kunt nu verbinding maken met uw VM door uw openbare IP-adres en poortnummer van het vereiste VM-exemplaar op te geven, zoals is gedaan in de voorgaande opdrachten. Wanneer u hierom wordt gevraagd, voert u de referenties in die zijn gebruikt bij het maken van de schaalset. Als u Azure Cloud Shell gebruikt, voert u deze stap uit vanaf een lokale PowerShell-prompt of vanuit de Extern bureaublad-client. In het volgende voorbeeld wordt verbinding gemaakt met het VM-exemplaar *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Open een lokale PowerShell-sessie op het VM-exemplaar en bekijk de gekoppelde schijven met [Get-Disk](/powershell/module/storage/get-disk):

```powershell
Get-Disk
```

In de volgende voorbeelduitvoer ziet u dat de drie gegevensschijven zijn gekoppeld aan het VM-exemplaar.

```powershell
Number  Friendly Name      HealthStatus  OperationalStatus  Total Size  Partition Style
------  -------------      ------------  -----------------  ----------  ---------------
0       Virtual HD         Healthy       Online                 127 GB  MBR
1       Virtual HD         Healthy       Online                  14 GB  MBR
2       Msft Virtual Disk  Healthy       Online                  64 GB  MBR
3       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
4       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
```

Onderzoek de bestandssystemen en koppelpunten op het VM-exemplaar als volgt:

```powershell
Get-Partition
```

In de volgende voorbeelduitvoer ziet u dat er stationsletters zijn toegewezen aan de drie gegevensschijven:

```powershell
   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000001

PartitionNumber  DriveLetter  Offset   Size  Type
---------------  -----------  ------   ----  ----
1                F            1048576  64 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000002

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                G            1048576  128 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000003

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                H            1048576  128 GB  IFS
```

De schijven in elk VM-exemplaar in uw schaalset worden automatisch op dezelfde manier voorbereid. Als de schaalset wordt opgeschaald, worden de vereiste gegevensschijven gekoppeld aan de nieuwe VM-exemplaren. De aangepaste scriptextensie wordt ook uitgevoerd om de schijven automatisch voor te bereiden.

Sluit de sessie van Extern bureaublad met het VM-exemplaar.


## <a name="list-attached-disks"></a>Gekoppelde schijven opvragen
Gebruik [Get-AzVmss](/powershell/module/az.compute/get-azvmss) als volgt om informatie op te vragen over schijven die zijn gekoppeld aan een schaalset:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
```

Onder de eigenschap *VirtualMachineProfile.StorageProfile* wordt de lijst met *DataDisks* weergegeven. U ziet vervolgens gegevens van de grootte van de schijf, de opslaglaag en het LUN (Logical Unit Number). In de volgende voorbeelduitvoer ziet u gegevens van de drie gegevensschijven die aan het VM-exemplaar zijn gekoppeld:

```powershell
DataDisks[0]                            :
  Lun                                   : 0
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 64
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[1]                            :
  Lun                                   : 1
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[2]                            :
  Lun                                   : 2
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
```


## <a name="detach-a-disk"></a>Een schijf loskoppelen
Wanneer u een bepaalde schijf niet meer nodig hebt, kunt u deze loskoppelen van de schaalset. De schijf wordt dan verwijderd uit alle VM-exemplaren in de schaalset. Als u een schijf wilt loskoppelen van een schaalset, gebruikt u [Remove-AzVmssDataDisk](/powershell/module/az.compute/remove-azvmssdatadisk) en geeft u het LUN van de schijf op. De LUN's worden weergegeven in de uitvoer van [Get-AzVmss](/powershell/module/az.compute/get-azvmss) in de vorige sectie. In het volgende voorbeeld wordt het LUN *3* losgekoppeld van de schaalset:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Detach a disk from the scale set
Remove-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -Lun 2

# Update the scale set and detach the disk from the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="clean-up-resources"></a>Resources opschonen
Als u de schaalset en schijven wilt verwijderen, verwijdert u de resourcegroep en alle bijbehorende resources met [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). De parameter `-Force` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen. De parameter `-AsJob` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u schijven maakt en gebruikt met schaalsets met Azure PowerShell:

> [!div class="checklist"]
> * Besturingssysteemschijven en tijdelijke schijven
> * Gegevensschijven
> * Standard- en Premium-schijven
> * Schijfprestaties
> * Gegevensschijven koppelen en voorbereiden

Ga naar de volgende zelfstudie voor informatie over het gebruik van een aangepaste installatiekopie voor de VM-exemplaren in uw schaalset.

> [!div class="nextstepaction"]
> [Een aangepaste installatiekopie gebruiken voor VM-exemplaren in een schaalset](tutorial-use-custom-image-powershell.md)