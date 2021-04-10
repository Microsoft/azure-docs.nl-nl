---
title: Virtuele Windows-machines maken en beheren in azure die gebruikmaken van meerdere Nic's
description: Meer informatie over het maken en beheren van een Windows-VM waaraan meerdere Nic's zijn gekoppeld met behulp van Azure PowerShell-of Resource Manager-sjablonen.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 2664d175818a6e29dcd3f704c6938987bae050cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555173"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Een virtuele Windows-machine met meerdere Nic's maken en beheren
Aan virtuele machines (Vm's) in azure kunnen meerdere Nic's (virtuele netwerk interface kaarten) zijn gekoppeld. Een veelvoorkomend scenario is om verschillende subnetten te hebben voor de connectiviteit van front-end-en back-end. U kunt meerdere Nic's aan een virtuele machine koppelen aan meerdere subnetten, maar deze subnetten moeten zich allemaal in hetzelfde virtuele netwerk (vNet) bevinden. In dit artikel vindt u informatie over het maken van een VM waaraan meerdere Nic's zijn gekoppeld. U leert ook hoe u Nic's kunt toevoegen aan of verwijderen uit een bestaande virtuele machine. Verschillende [VM-grootten](../sizes.md) ondersteunen een variërend aantal nic's, dus grootte van uw virtuele machine dienovereenkomstig.

## <a name="prerequisites"></a>Vereisten

Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter namen zijn *myResourceGroup*, *myVnet* en *myVM*.

 

## <a name="create-a-vm-with-multiple-nics"></a>Een virtuele machine met meerdere NIC's maken
Maak eerst een resource groep. In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *eastus* :

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Virtueel netwerk en subnetten maken
Een veelvoorkomend scenario is dat een virtueel netwerk twee of meer subnetten heeft. Eén subnet is mogelijk voor front-end verkeer, de andere voor back-end-verkeer. Als u verbinding wilt maken met beide subnetten, gebruikt u vervolgens meerdere Nic's op uw VM.

1. Definieer twee subnetten van het virtuele netwerk met [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). In het volgende voor beeld worden de subnetten gedefinieerd voor *mySubnetFrontEnd* en *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Maak uw virtuele netwerk en subnetten met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam *myVnet*:

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Meerdere Nic's maken
Maak twee Nic's met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Sluit één NIC aan op het front-end-subnet en één NIC aan het back-end-subnet. In het volgende voor beeld worden Nic's gemaakt met de naam *myNic1* en *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Doorgaans maakt u ook een [netwerk beveiligings groep](../../virtual-network/network-security-groups-overview.md) om het netwerk verkeer naar de virtuele machine te filteren en een [Load Balancer](../../load-balancer/load-balancer-overview.md) om verkeer over meerdere vm's te distribueren.

### <a name="create-the-virtual-machine"></a>De virtuele machine maken
Begin nu met het maken van de VM-configuratie. Voor elke VM-grootte geldt een limiet voor het totale aantal Nic's dat u aan een virtuele machine kunt toevoegen. Zie [Windows VM-grootten](../sizes.md)voor meer informatie.

1. Stel uw VM-referenties als volgt in op de `$cred` variabele:

    ```powershell
    $cred = Get-Credential
    ```

2. Definieer uw virtuele machine met [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). In het volgende voor beeld wordt een virtuele machine met de naam *myVM* gedefinieerd en wordt een VM-grootte gebruikt die meer dan twee nic's ondersteunt (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Maak de rest van uw VM-configuratie met [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) en [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). In het volgende voor beeld wordt een virtuele machine met Windows Server 2016 gemaakt:

    ```powershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Koppel de twee Nic's die u eerder hebt gemaakt met [add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Maak uw virtuele machine met [New-AzVM](/powershell/module/az.compute/new-azvm):

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Voeg routes voor secundaire Nic's toe aan het besturings systeem door de stappen in [het besturings systeem configureren voor meerdere nic's](#configure-guest-os-for-multiple-nics)te volt ooien.

## <a name="add-a-nic-to-an-existing-vm"></a>Een NIC toevoegen aan een bestaande virtuele machine
U kunt een virtuele NIC toevoegen aan een bestaande virtuele machine door de toewijzing van de VM ongedaan te maken, de virtuele NIC toe te voegen en vervolgens de VM te starten. Verschillende [VM-grootten](../sizes.md) ondersteunen een variërend aantal nic's, dus grootte van uw virtuele machine dienovereenkomstig. Indien nodig kunt u [de grootte van een virtuele machine wijzigen](resize-vm.md).

1. Hef de toewijzing van de virtuele machine op met [AzVM](/powershell/module/az.compute/stop-azvm). In het volgende voor beeld wordt de toewijzing van de virtuele machine met de naam *myVM* in *myResourceGroup* ongedaan gemaakt:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. De bestaande configuratie van de virtuele machine ophalen met [Get-AzVm](/powershell/module/az.compute/get-azvm). In het volgende voor beeld wordt informatie opgehaald voor de virtuele machine met de naam *myVM* in *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. In het volgende voor beeld wordt een virtuele NIC gemaakt met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) met de naam *myNic3* die is gekoppeld aan *mySubnetBackEnd*. De virtuele NIC wordt vervolgens gekoppeld aan de VM met de naam *myVM* in *myResourceGroup* met [add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Primaire virtuele Nic's
    Een van de Nic's op een multi-NIC-VM moet primair zijn. Als een van de bestaande virtuele Nic's op de VM al als primair is ingesteld, kunt u deze stap overs Laan. In het volgende voor beeld wordt ervan uitgegaan dat twee virtuele Nic's nu aanwezig zijn op een VM en u de eerste NIC ( `[0]` ) als primair wilt toevoegen:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Start de VM met [Start-AzVm](/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Voeg routes voor secundaire Nic's toe aan het besturings systeem door de stappen in [het besturings systeem configureren voor meerdere nic's](#configure-guest-os-for-multiple-nics)te volt ooien.

## <a name="remove-a-nic-from-an-existing-vm"></a>Een NIC verwijderen van een bestaande virtuele machine
Als u een virtuele NIC van een bestaande VM wilt verwijderen, moet u de toewijzing van de VM ongedaan maken, de virtuele NIC verwijderen en vervolgens de VM starten.

1. Hef de toewijzing van de virtuele machine op met [AzVM](/powershell/module/az.compute/stop-azvm). In het volgende voor beeld wordt de toewijzing van de virtuele machine met de naam *myVM* in *myResourceGroup* ongedaan gemaakt:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. De bestaande configuratie van de virtuele machine ophalen met [Get-AzVm](/powershell/module/az.compute/get-azvm). In het volgende voor beeld wordt informatie opgehaald voor de virtuele machine met de naam *myVM* in *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Informatie over de NIC verwijderen met [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface). In het volgende voor beeld wordt informatie over *myNic3* opgehaald:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Verwijder de NIC met [Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface) en werk de virtuele machine bij met [Update-AzVm](/powershell/module/az.compute/update-azvm). In het volgende voor beeld wordt *myNic3* verwijderd zoals verkregen `$nicId` in de vorige stap:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Start de VM met [Start-AzVm](/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Meerdere Nic's maken met sjablonen
Azure Resource Manager sjablonen bieden een manier om meerdere exemplaren van een resource te maken tijdens de implementatie, zoals het maken van meerdere Nic's. Resource Manager-sjablonen gebruiken declaratieve JSON-bestanden om uw omgeving te definiëren. Zie [overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)voor meer informatie. U kunt *kopiëren* gebruiken om het aantal te maken exemplaren op te geven:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Zie [meerdere exemplaren maken met behulp van *kopiëren*](../../azure-resource-manager/templates/copy-resources.md)voor meer informatie. 

U kunt ook gebruiken `copyIndex()` om een nummer toe te voegen aan een resource naam. Vervolgens kunt u *myNic1*, *MyNic2* , enzovoort maken. De volgende code toont een voor beeld van het toevoegen van de index waarde:

```json
"name": "[concat('myNic', copyIndex())]", 
```

U kunt een volledig voor beeld van [het maken van meerdere nic's lezen met behulp van Resource Manager-sjablonen](../../virtual-network/template-samples.md).

Voeg routes voor secundaire Nic's toe aan het besturings systeem door de stappen in [het besturings systeem configureren voor meerdere nic's](#configure-guest-os-for-multiple-nics)te volt ooien.

## <a name="configure-guest-os-for-multiple-nics"></a>Gast besturingssysteem voor meerdere Nic's configureren

Azure wijst een standaard gateway toe aan de eerste (primaire) netwerk interface die is gekoppeld aan de virtuele machine. Azure wijst geen standaardgateway toe aan extra (secundaire) netwerkinterfaces die zijn gekoppeld aan een virtuele machine. Daarom kunt u standaard niet communiceren met resources buiten het subnet waarin een secundaire netwerkinterface zich bevindt. Secundaire netwerk interfaces kunnen echter communiceren met resources buiten hun subnet, hoewel de stappen om communicatie in te scha kelen, verschillend zijn voor verschillende besturings systemen.

1. Voer vanaf een Windows-opdracht prompt de `route print` opdracht uit. Dit resulteert in een uitvoer die vergelijkbaar is met de volgende uitvoer voor een virtuele machine met twee gekoppelde netwerk interfaces:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    In dit voor beeld is **Microsoft Hyper-V netwerk Adapter #4** (interface 7) de secundaire netwerk interface waaraan geen standaard gateway is toegewezen.

2. Voer vanaf een opdracht prompt de `ipconfig` opdracht uit om te zien welk IP-adres is toegewezen aan de secundaire netwerk interface. In dit voor beeld is 192.168.2.4 toegewezen aan interface 7. Er wordt geen standaard gateway adres geretourneerd voor de secundaire netwerk interface.

3. Als u al het verkeer wilt routeren dat bestemd is voor adressen buiten het subnet van de secundaire netwerk interface naar de gateway voor het subnet, voert u de volgende opdracht uit:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Het gateway adres voor het subnet is het eerste IP-adres (dat eindigt op 1) in het adres bereik dat is gedefinieerd voor het subnet. Als u niet alle verkeer buiten het subnet wilt routeren, kunt u in plaats daarvan afzonderlijke routes toevoegen aan specifieke bestemmingen. Als u bijvoorbeeld alleen verkeer van de secundaire netwerk interface naar het 192.168.3.0-netwerk wilt routeren, voert u de volgende opdracht in:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Als u geslaagde communicatie met een resource in het 192.168.3.0-netwerk wilt bevestigen, voert u bijvoorbeeld de volgende opdracht in om 192.168.3.4 te pingen met interface 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Mogelijk moet u ICMP openen via de Windows Firewall van het apparaat dat u wilt pingen met de volgende opdracht:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Als u wilt bevestigen dat de toegevoegde route zich in de route tabel bevindt, voert u de `route print` opdracht in, die de uitvoer retourneert, zoals in de volgende tekst:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    De route die wordt vermeld bij *192.168.1.1* onder **Gateway**, is de route die standaard is voor de primaire netwerk interface. De route met *192.168.2.1* onder **Gateway** is de route die u hebt toegevoegd.

## <a name="next-steps"></a>Volgende stappen
Controleer de [grootte van Windows-vm's](../sizes.md) wanneer u een VM met meerdere nic's wilt maken. Let op het maximum aantal Nic's dat elke VM-grootte ondersteunt.