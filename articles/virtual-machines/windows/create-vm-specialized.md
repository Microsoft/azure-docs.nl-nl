---
title: Een Windows-VM maken op basis van een gespecialiseerde VHD in azure
description: Maak een nieuwe Windows-VM door een speciale beheerde schijf te koppelen als de besturingssysteem schijf met behulp van het Resource Manager-implementatie model.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: cddc7f4f453f22b0cb36b1d3a1e9c2fba2dcabaf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455097"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Een virtuele Windows-machine maken vanaf een speciale schijf met PowerShell

Maak een nieuwe virtuele machine door een speciale beheerde schijf als de besturingssysteem schijf te koppelen. Een gespecialiseerde schijf is een kopie van een virtuele harde schijf (VHD) van een bestaande VM die de gebruikers accounts, toepassingen en andere status gegevens van uw oorspronkelijke virtuele machine bevat. 

U hebt verschillende mogelijkheden:
* [Gebruik een bestaande beheerde schijf](#option-1-use-an-existing-disk). Deze optie is handig als u een VM hebt die niet goed werkt. U kunt de virtuele machine verwijderen en vervolgens de beheerde schijf opnieuw gebruiken om een nieuwe virtuele machine te maken. 
* [Een VHD uploaden](#option-2-upload-a-specialized-vhd) 
* [Een bestaande Azure-VM kopiëren met behulp van moment opnamen](#option-3-copy-an-existing-azure-vm)

U kunt ook de Azure Portal gebruiken om [een nieuwe VM te maken op basis van een gespecialiseerde VHD](create-vm-specialized-portal.md).

In dit artikel wordt beschreven hoe u beheerde schijven gebruikt. Zie [een virtuele machine maken op basis van een speciale VHD in een opslag account](/previous-versions/azure/virtual-machines/windows/sa-create-vm-specialized)als u een oudere implementatie hebt waarvoor een opslag account is vereist.

> [!IMPORTANT]
> 
> Wanneer u een speciale schijf gebruikt om een nieuwe virtuele machine te maken, behoudt de nieuwe VM de computer naam van de oorspronkelijke VM. Andere computerspecifieke informatie (bijvoorbeeld CMID) wordt ook bewaard en, in sommige gevallen, kan deze dubbele informatie problemen veroorzaken. Wanneer u een virtuele machine kopieert, moet u zich bewust zijn van de typen computerspecifieke informatie die uw toepassingen gebruiken.  
> Gebruik daarom geen gespecialiseerde schijf als u meerdere Vm's wilt maken. Maak in plaats daarvan [een installatie kopie](capture-image-resource.md) voor grotere implementaties en [gebruik vervolgens de installatie kopie om meerdere vm's te maken](create-vm-generalized-managed.md).

We raden u aan om het aantal gelijktijdige implementaties te beperken tot 20 virtuele machines vanaf één VHD of moment opname. 

## <a name="option-1-use-an-existing-disk"></a>Optie 1: een bestaande schijf gebruiken

Als u een virtuele machine hebt verwijderd en u de besturingssysteem schijf opnieuw wilt gebruiken om een nieuwe virtuele machine te maken, gebruikt u [Get-AzDisk](/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
U kunt deze schijf nu als de besturingssysteem schijf aan een [nieuwe virtuele machine](#create-the-new-vm)koppelen.

## <a name="option-2-upload-a-specialized-vhd"></a>Optie 2: een speciale VHD uploaden

U kunt de VHD uploaden vanaf een gespecialiseerde virtuele machine die is gemaakt met een on-premises Virtualization tool, zoals Hyper-V, of een VM die is geëxporteerd uit een andere cloud.

### <a name="prepare-the-vm"></a>De virtuele machine voorbereiden
Gebruik de VHD als-is om een nieuwe virtuele machine te maken. 
  
  * [Bereid een Windows VHD voor om te uploaden naar Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Generaliseer de virtuele machine **niet** met behulp van Sysprep.
  * Verwijder alle hulpprogram ma's voor gast-virtualisatie en de agents die zijn geïnstalleerd op de virtuele machine (zoals VMware-hulpprogram ma's).
  * Zorg ervoor dat de virtuele machine is geconfigureerd om het IP-adres en DNS-instellingen van DHCP op te halen. Dit zorgt ervoor dat de server een IP-adres binnen het virtuele netwerk verkrijgt wanneer het wordt gestart. 


### <a name="upload-the-vhd"></a>De VHD uploaden

U kunt nu een VHD rechtstreeks uploaden naar een beheerde schijf. Zie [een VHD uploaden naar Azure met Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md)voor instructies.

## <a name="option-3-copy-an-existing-azure-vm"></a>Optie 3: een bestaande virtuele machine van Azure kopiëren

U kunt een kopie maken van een virtuele machine die gebruikmaakt van beheerde schijven door een moment opname van de virtuele machine uit te voeren en vervolgens met die moment opname een nieuwe beheerde schijf en een nieuwe virtuele machine te maken.

Als u een bestaande VM wilt kopiëren naar een andere regio, wilt u mogelijk azcopy gebruiken om [een kopie van een schijf in een andere regio te maken](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Een moment opname maken van de besturingssysteem schijf

U kunt een moment opname maken van een volledige virtuele machine (inclusief alle schijven) of van slechts één schijf. De volgende stappen laten zien hoe u een moment opname maakt van de besturingssysteem schijf van de virtuele machine met de cmdlet [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) . 

Stel eerst een aantal para meters in. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Haal het VM-object op.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
De naam van de besturingssysteem schijf ophalen.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

De configuratie van de moment opname maken. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Neem de moment opname.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Als u deze moment opname wilt gebruiken om een virtuele machine te maken die hoog moet worden uitgevoerd, voegt u de para meter toe `-AccountType Premium_LRS` aan de opdracht New-AzSnapshotConfig. Met deze para meter wordt de moment opname gemaakt zodat deze wordt opgeslagen als Premium-beheerde schijf. Premium-Managed Disks zijn duurder dan standaard. Zorg er dus voor dat u Premium nodig hebt voordat u deze para meter gebruikt.

### <a name="create-a-new-disk-from-the-snapshot"></a>Een nieuwe schijf maken op basis van de moment opname

Een beheerde schijf maken op basis van de moment opname met behulp van [New-AzDisk](/powershell/module/az.compute/new-azdisk). In dit voor beeld wordt *myOSDisk* gebruikt voor de naam van de schijf.

Maak een nieuwe resource groep voor de nieuwe virtuele machine.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Stel de naam van de besturingssysteem schijf in. 

```powershell
$osDiskName = 'myOsDisk'
```

Maak de beheerde schijf.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>De nieuwe VM maken 

Maak netwerken en andere VM-resources die moeten worden gebruikt door de nieuwe virtuele machine.

### <a name="create-the-subnet-and-virtual-network"></a>Het subnet en het virtuele netwerk maken

Maak het [virtuele netwerk](../../virtual-network/virtual-networks-overview.md) en het subnet voor de VM.

1. Maak het subnet. In dit voor beeld wordt een subnet met de naam *mySubNet* gemaakt in de resource groep *myDestinationResourceGroup* en wordt het adres voorvoegsel van het subnet ingesteld op *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Maak het virtuele netwerk. In dit voor beeld wordt de naam van het virtuele netwerk ingesteld op *myVnetName*, de locatie van *VS-West* en het adres voorvoegsel voor het virtuele netwerk naar *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>De netwerk beveiligings groep en een RDP-regel maken
Als u zich wilt aanmelden bij uw virtuele machine met RDP (Remote Desktop Protocol), moet u een beveiligings regel hebben waarmee RDP-toegang wordt toegestaan op poort 3389. In ons voor beeld is de VHD voor de nieuwe virtuele machine gemaakt op basis van een bestaande gespecialiseerde virtuele machine, zodat u een account kunt gebruiken dat aanwezig is op de bron-VM voor RDP.

In dit voor beeld wordt de naam van de netwerk beveiligings groep (NSG) ingesteld op *mijnnbg* en de naam van de RDP-regel in *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Zie [poorten openen voor een virtuele machine in azure met behulp van Power shell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over eind punten en NSG regels.

### <a name="create-a-public-ip-address-and-nic"></a>Een openbaar IP-adres en een NIC maken
Als u communicatie met de virtuele machine in het virtuele netwerk wilt inschakelen, hebt u een [openbaar IP-adres](../../virtual-network/public-ip-addresses.md) en een netwerk interface nodig.

1. Maak het open bare IP-adres. In dit voor beeld is de naam van het open bare IP-adres ingesteld op *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Maak de NIC. In dit voor beeld is de naam van de NIC ingesteld op *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>De naam en grootte van de virtuele machine instellen

In dit voor beeld wordt de naam van de virtuele machine ingesteld op *myVM* en de VM-grootte *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>De NIC toevoegen
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>De besturingssysteem schijf toevoegen 

Voeg de besturingssysteem schijf toe aan de configuratie met behulp van [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk). In dit voor beeld wordt de grootte van de schijf ingesteld op *128 GB* en wordt de beheerde schijf als een *Windows* -besturingssysteem schijf gekoppeld.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>De virtuele machine volt ooien 

Maak de virtuele machine met behulp van [New-AzVM](/powershell/module/az.compute/new-azvm) met de configuraties die we zojuist hebben gemaakt.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Als deze opdracht is geslaagd, ziet u uitvoer als volgt:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Controleren of de virtuele machine is gemaakt
U ziet de zojuist gemaakte vm in de [Azure Portal](https://portal.azure.com) onder **Browse**  >  **virtuele machines** bladeren of met behulp van de volgende Power shell-opdrachten.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Volgende stappen
Meld u aan bij de nieuwe virtuele machine. Zie [verbinding maken en aanmelden bij een virtuele Azure-machine met Windows](connect-logon.md)voor meer informatie.