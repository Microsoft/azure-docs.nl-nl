---
title: Windows-VM maken met versneld netwerken-Azure PowerShell
description: Maak een virtuele Windows-machine (VM) met versneld netwerken om de netwerk prestaties aanzienlijk te verbeteren.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: b0ebb75530858a589c3166e21261e2f737fff50d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919966"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Een Windows-VM met versneld netwerken maken met behulp van Azure PowerShell

In deze zelf studie leert u hoe u een virtuele Windows-machine (VM) kunt maken met versneld netwerken.

> [!NOTE]
> Zie een virtuele Linux-machine [met versneld netwerken maken](create-vm-accelerated-networking-cli.md)voor het gebruik van versneld netwerken met een Linux-VM.

Versneld netwerken maken gebruik van I/O-virtualisatie met één hoofdmap (SR-IOV) naar een virtuele machine, waardoor de netwerk prestaties aanzienlijk worden verbeterd. Dit pad met hoge prestaties omzeilt de host van het gegevenspad, waardoor latentie, jitter en CPU-gebruik worden verminderd voor de meest veeleisende netwerk workloads op ondersteunde VM-typen. In het volgende diagram ziet u hoe twee Vm's communiceren met en zonder versneld netwerk:

![Communicatie tussen virtuele machines van Azure met en zonder versnelde netwerken](./media/create-vm-accelerated-networking/accelerated-networking.png)

Zonder versneld netwerk moeten al het netwerk verkeer van en naar de virtuele machine de host en de Virtual Switch door lopen. De virtuele switch biedt alle beleids afdwinging, zoals netwerk beveiligings groepen, Toegangs beheer lijsten, isolatie en andere gevirtualiseerde netwerk services voor netwerk verkeer.

> [!NOTE]
> Zie voor meer informatie over virtuele switches [Hyper-V Virtual Switch](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch).

Met versneld netwerken arriveert het netwerk verkeer op de netwerk interface van de VM (NIC) en wordt vervolgens doorgestuurd naar de virtuele machine. Alle netwerk beleidsregels waarmee de virtuele switch wordt toegepast, worden nu geoffload en toegepast in hardware. Omdat beleid wordt toegepast in hardware, kan de NIC netwerk verkeer rechtstreeks door sturen naar de virtuele machine. De NIC omzeilt de host en de virtuele switch, terwijl alle beleids regels die op de host worden toegepast, worden bijgehouden.

De voor delen van versneld netwerken zijn alleen van toepassing op de virtuele machine waarop deze is ingeschakeld. Voor de beste resultaten schakelt u deze functie in op ten minste twee Vm's die zijn verbonden met hetzelfde virtuele Azure-netwerk. Bij de communicatie tussen virtuele netwerken of het verbinden van on-premises, heeft deze functie minimale gevolgen voor de algehele latentie.

## <a name="benefits"></a>Voordelen

- **Lagere latentie/hogere pakketten per seconde (PPS)**: als u de virtuele switch uit het gegevenspad verwijdert, worden de tijd pakketten die in de host worden besteed, verwijderd voor beleids verwerking. Het verhoogt ook het aantal pakketten dat kan worden verwerkt in de virtuele machine.

- **Verminderde jitter**: virtuele switch verwerking is afhankelijk van de hoeveelheid beleid die moet worden toegepast. Het is ook afhankelijk van de werk belasting van de CPU die de verwerking uitvoert. Door het beleid afdwingen naar de hardware te verwijderen, verwijdert u die variabiliteit door pakketten rechtstreeks aan de virtuele machine te leveren. Offloading verwijdert ook de communicatie tussen de host en de VM, alle software-interrupts en alle context switches.

- **Verminderd CPU-gebruik**: het overs laan van de virtuele switch in de host leidt tot minder CPU-gebruik voor het verwerken van netwerk verkeer.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

De volgende distributies worden direct ondersteund vanuit de Azure-galerie:

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>Beperkingen en beperkingen

### <a name="supported-vm-instances"></a>Ondersteunde VM-exemplaren

Versnelde netwerken worden ondersteund in de meeste algemene doel stellingen en met Compute geoptimaliseerde exemplaar grootten met twee of meer Vcpu's. Op instanties die HyperThreading ondersteunen, wordt versneld netwerken ondersteund op VM-exemplaren met vier of meer Vcpu's. 

Ondersteuning voor versneld netwerken vindt u in de documentatie over de grootte van de afzonderlijke [virtuele machines](../virtual-machines/sizes.md) . 

### <a name="custom-images"></a>Aangepaste installatiekopieën

Als u een aangepaste installatie kopie gebruikt en uw installatie kopie versneld netwerken ondersteunt, moet u ervoor zorgen dat u beschikt over de vereiste Stuur Programma's die werken met Mellanox Connectx-3 en Connectx-4 LX-Nic's in Azure.

### <a name="regions"></a>Regio's

Versneld netwerken is beschikbaar in alle wereld wijde Azure-regio's en Azure Government Cloud.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Versnelde netwerken inschakelen op een actieve virtuele machine

Voor een ondersteunde VM-grootte zonder versneld netwerken is ingeschakeld, kan de functie alleen worden ingeschakeld wanneer deze is gestopt en de toewijzing ongedaan wordt gemaakt.

### <a name="deployment-through-azure-resource-manager"></a>Implementatie via Azure Resource Manager

Virtuele machines (klassiek) kunnen niet worden geïmplementeerd met versneld netwerken.

## <a name="vm-creation-using-the-portal"></a>VM maken met behulp van de portal

Hoewel dit artikel stappen bevat voor het maken van een virtuele machine met versneld netwerken met behulp van Azure PowerShell, kunt u ook [de Azure Portal gebruiken om een VM te maken](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) die versneld netwerken mogelijk maakt. Wanneer u een virtuele machine in de portal maakt, kiest u op de pagina **een VM maken** het tabblad **netwerk** . Dit tabblad bevat een optie voor **versneld netwerken**. Als u een [ondersteund besturings systeem](#supported-operating-systems) en VM- [grootte](#supported-vm-instances)hebt gekozen, wordt deze optie automatisch ingesteld op **aan**. Anders is de optie ingesteld op **uit** en in azure wordt de reden weer gegeven waarom deze niet kan worden ingeschakeld.

> [!NOTE]
> Alleen ondersteunde besturings systemen kunnen worden ingeschakeld via de portal. Als u een aangepaste installatie kopie gebruikt en uw installatie kopie versneld netwerken ondersteunt, maakt u uw virtuele machine met CLI of Power shell. 

Nadat u de virtuele machine hebt gemaakt, kunt u controleren of versneld netwerken zijn ingeschakeld. Volg deze instructies:

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw virtuele machines te beheren. Zoek en selecteer **virtuele machines**.

2. Kies uw nieuwe VM in de lijst met virtuele machines.

3. Kies in de menu balk van de VM de optie **netwerken**.

In de gegevens van de netwerk interface, naast het label **versneld netwerk** , wordt de portal **uitgeschakeld** of **ingeschakeld** voor de versnelde netwerk status.

## <a name="vm-creation-using-powershell"></a>VM maken met Power shell

Installeer [Azure PowerShell](/powershell/azure/install-az-ps) versie 1.0.0 of hoger voordat u doorgaat. Voer uit om de momenteel geïnstalleerde versie te vinden `Get-Module -ListAvailable Az` . Als u wilt installeren of upgraden, installeert u de nieuwste versie van de AZ-module vanaf de [PowerShell Gallery](https://www.powershellgallery.com/packages/Az). Meld u in een Power shell-sessie aan bij een Azure [-account met Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter namen zijn *myResourceGroup*, *myNic* en *myVM*.

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Met de volgende opdracht maakt u een resource groep met de naam *myResourceGroup* in de locatie *middenus* :

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. Maak een subnet-configuratie met [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). Met de volgende opdracht maakt u een subnet met de naam *mySubnet*:

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)met het *mySubnet* -subnet.

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

1. Maak een netwerkbeveiligingsgroepsregel met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

    ```azurepowershell
    $rdp = New-AzNetworkSecurityRuleConfig `
        -Name 'Allow-RDP-All' `
        -Description 'Allow RDP' `
        -Access Allow `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 100 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389
    ```

2. Maak een netwerk beveiligings groep met [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) en wijs hieraan de regel *Allow-RDP-all* Security toe. Afgezien van de regel voor het *toestaan van RDP-alle* , bevat de netwerk beveiligings groep verschillende standaard regels. Met één standaard regel wordt alle binnenkomende toegang van Internet uitgeschakeld. Zodra de app is gemaakt, wordt de regel voor het *toestaan van RDP-alle* aan de netwerk beveiligings groep toegewezen, zodat u op afstand verbinding kunt maken met de virtuele machine.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. Koppel de netwerk beveiligings groep aan het *mySubnet* -subnet met [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). De regel in de netwerk beveiligings groep is effectief voor alle resources die in het subnet zijn geïmplementeerd.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Een netwerk interface met versneld netwerken maken

1. Maak een openbaar IP-adres met [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Een openbaar IP-adres is niet nodig als u niet van plan bent om toegang te krijgen tot de virtuele machine via internet. Het is echter wel nodig om de stappen in dit artikel uit te voeren.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. Maak een netwerk interface met [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) met versneld netwerken ingeschakeld en wijs het open bare IP-adres toe aan de netwerk interface. In het volgende voor beeld wordt een netwerk interface met de naam *myNic* gemaakt in het *mySubnet* -subnet van het virtuele netwerk van *myVnet* , waarbij u het open bare IP-adres van *myPublicIp* hieraan toewijst:

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>Een virtuele machine maken en de netwerk interface koppelen

1. Stel uw VM-referenties in op de `$cred` variabele met [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential), waarin u wordt gevraagd om u aan te melden:

    ```azurepowershell
    $cred = Get-Credential
    ```

2. Definieer uw virtuele machine met [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Met de volgende opdracht wordt een virtuele machine gedefinieerd met de naam *myVM* met een VM-grootte die ondersteuning biedt voor versneld netwerken (*Standard_DS4_v2*):

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    Zie [Windows VM-grootten](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor een lijst met alle VM-grootten en-kenmerken.

3. Maak de rest van uw VM-configuratie met [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) en [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Met de volgende opdracht maakt u een virtuele machine met Windows Server 2016:

    ```azurepowershell
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

4. Koppel de netwerk interface die u eerder hebt gemaakt met [add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. Maak een virtuele machine met [New-AzVM](/powershell/module/az.compute/new-azvm).

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Controleren of de Ethernet-controller is geïnstalleerd in de Windows-VM

Nadat u de virtuele machine in azure hebt gemaakt, maakt u verbinding met de virtuele machine en controleert u of de Ethernet-controller is geïnstalleerd in Windows.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw virtuele machines te beheren. Zoek en selecteer **virtuele machines**.

2. Kies uw nieuwe VM in de lijst met virtuele machines.

3. Als de **status** van de virtuele machine wordt **gemaakt** in de VM-overzichts pagina, wacht u totdat de virtuele machine is gemaakt door Azure. De **status** wordt gewijzigd in **actief** nadat het maken van de VM is voltooid.

4. Selecteer op de werk balk van de VM-overzicht de optie RDP-bestand voor het **verbinding maken**  >    >  .

5. Open het RDP-bestand en meld u vervolgens aan bij de virtuele machine met de referenties die u hebt opgegeven in de sectie [een virtuele machine maken en de netwerk interface koppelen](#create-a-vm-and-attach-the-network-interface) . Als u nooit hebt verbonden met een Windows-VM in azure, raadpleegt u [verbinding maken met de virtuele machine](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).

6. Nadat de extern bureau blad-sessie voor uw virtuele machine wordt weer gegeven, klikt u met de rechter muisknop op de knop Start van Windows en kiest u **Apparaatbeheer**.

7. Vouw in het venster **Apparaatbeheer** het knoop punt **netwerk adapters** uit.

8. Bevestig dat de **Mellanox connectx-3 virtuele function Ethernet-adapter** wordt weer gegeven, zoals wordt weer gegeven in de volgende afbeelding:

    ![Mellanox Connectx-3 virtuele functie Ethernet-adapter, nieuwe netwerk adapter voor versneld netwerken, Apparaatbeheer](./media/create-vm-accelerated-networking/device-manager.png)

Versneld netwerken zijn nu ingeschakeld voor uw VM.

> [!NOTE]
> Als de Mellanox-adapter niet kan worden gestart, opent u een beheerders prompt in de extern bureau blad-sessie en voert u de volgende opdracht in:
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>Versneld netwerken op bestaande Vm's inschakelen

Als u een virtuele machine zonder versneld netwerk hebt gemaakt, kunt u deze functie inschakelen op een bestaande virtuele machine. De virtuele machine moet versneld netwerken ondersteunen door aan de volgende vereisten te voldoen, die ook hierboven worden beschreven:

* De virtuele machine moet een ondersteunde grootte voor versneld netwerken zijn.
* De VM moet een ondersteunde installatie kopie van Azure Gallery zijn (en kernel-versie voor Linux).
* Alle virtuele machines in een beschikbaarheidsset of een schaalset voor virtuele machines moeten worden gestopt of worden vrijgegeven voordat u versneld netwerken op een NIC inschakelt.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>Afzonderlijke Vm's en virtuele machines in een beschikbaarheidsset

1. De virtuele machine stoppen of de toewijzing ervan ongedaan maken of, als er een beschikbaarheidsset is, alle virtuele machines in de set:

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > Wanneer u een virtuele machine afzonderlijk maakt, zonder een beschikbaarheidsset, hoeft u de afzonderlijke virtuele machine alleen te stoppen of te detoewijzen om versnelde netwerken in te scha kelen. Als uw virtuele machine is gemaakt met een beschikbaarheidsset, moet u alle virtuele machines in de beschikbaarheidsset stoppen of de toewijzing ervan ongedaan maken voordat u versneld netwerken op een van de Nic's inschakelt, zodat de Vm's eindigen op een cluster dat versneld netwerken ondersteunt. De vereiste voor het stoppen of ongedaan maken van de toewijzing is niet nodig als u versneld netwerken uitschakelt, omdat clusters die ondersteuning bieden voor versnelde netwerken ook compatibel zijn met Nic's die geen versneld netwerken gebruiken.

2. Versneld netwerken inschakelen op de NIC van uw VM:

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. Start de VM opnieuw op of, in een beschikbaarheidsset, alle virtuele machines in de set en controleer of versneld netwerken zijn ingeschakeld:

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>Schaalset voor virtuele machines

Een schaalset voor virtuele machines is iets anders, maar volgt dezelfde werk stroom.

1. De Vm's stoppen:

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. Werk de eigenschap versneld netwerk bij onder de netwerk interface:

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. Stel de toegepaste updates in op automatisch, zodat de wijzigingen onmiddellijk worden opgehaald:

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > Een schaalset heeft VM-upgrades waarmee updates worden toegepast met behulp van drie verschillende instellingen: automatisch, Rolling en hand matig. In deze instructies wordt het beleid ingesteld op automatisch, waardoor de schaalset de wijzigingen onmiddellijk ophaalt nadat deze opnieuw is opgestart.

4. Start de schaalset opnieuw:

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

Nadat de computer opnieuw is opgestart, wacht u totdat de upgrades zijn voltooid. Nadat de upgrades zijn uitgevoerd, wordt de virtuele functie (VF) weer gegeven in de VM. Zorg ervoor dat u een ondersteund besturings systeem en VM-grootte gebruikt.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Het formaat wijzigen van bestaande Vm's met versneld netwerken

Als op een virtuele machine versneld netwerken zijn ingeschakeld, kunt u de grootte van de VM alleen wijzigen in een VM die versneld netwerken ondersteunt.  

Het formaat van een virtuele machine met versneld netwerken kan niet worden gewijzigd in een VM-exemplaar dat geen ondersteuning biedt voor versnelde netwerken met behulp van de grootte van het formaat. In plaats daarvan wijzigt u het formaat van een van deze Vm's:

1. De virtuele machine stoppen of de toewijzing ongedaan maken. Als u een beschikbaarheidsset of schaalset wilt instellen, stopt of wijst u de toewijzing van alle virtuele machines in de beschikbaarheidsset of schaalset.

2. Schakel versnelde netwerken op de NIC van de virtuele machine uit. Schakel voor een beschikbaarheidsset of schaalset versneld netwerken uit op de Nic's van alle virtuele machines in de beschikbaarheidsset of schaalset.

3. Nadat u versneld netwerken hebt uitgeschakeld, verplaatst u de virtuele machine, beschikbaarheidsset of schaalset naar een nieuwe grootte die geen versneld netwerken ondersteunt, en start u deze opnieuw.