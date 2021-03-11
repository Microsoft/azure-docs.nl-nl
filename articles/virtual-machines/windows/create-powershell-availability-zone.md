---
title: Een virtuele machine in een zone maken met behulp van Azure PowerShell
description: Een virtuele machine maken in een beschikbaarheids zone met Azure PowerShell
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 7167c5806fde883a84878d4947dba9fd8c095f69
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558828"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-azure-powershell"></a>Een virtuele machine maken in een beschikbaarheids zone met behulp van Azure PowerShell

In dit artikel wordt beschreven hoe u Azure PowerShell gebruikt om in een Azure-beschikbaarheidszone een virtuele Azure-machine te maken waarop Windows Server 2016 wordt uitgevoerd. Een [beschikbaarheidszone](../../availability-zones/az-overview.md) is een fysiek afgescheiden zone in een Azure-regio. Gebruik beschikbaarheidszones om uw apps en gegevens te beschermen tegen het onwaarschijnlijke risico van een storing of het verloren gaan van een heel datacenter.

Als u een beschikbaarheidszone wilt gebruiken, maakt u een virtuele machine in een [ondersteunde Azure-regio](../../availability-zones/az-region.md).

 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

## <a name="check-vm-sku-availability"></a>Beschikbaarheid van VM-SKU controleren
De beschikbaarheid van VM-grootten, of SKU's, kan verschillen per regio en zone. Als hulp bij de planning voor het gebruik van de beschikbaarheidszones kunt u een overzicht maken van de beschikbare VM-SKU's per Azure-regio en zone. Hiermee zorgt u ervoor dat u een correcte VM-grootte kiest en de gewenste tolerantie in zones verkrijgt. Voor meer informatie over de verschillende VM-typen en -grootten raadpleegt u het [Overzicht van de VM-grootten](../sizes.md).

U kunt de beschikbare VM-SKU's bekijken met de opdracht [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku). Met het volgende voorbeeld wordt een lijst weergegeven van de beschikbare VM-SKU's in de regio *eastus2*:

```powershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

De uitvoer is vergelijkbaar met het volgende verkorte voorbeeld, waarin de beschikbaarheidszones worden weergegeven waarin elke VM-grootte beschikbaar is:

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In dit voorbeeld wordt er een resourcegroep met de naam *MyResourceGroup* gemaakt in de *eastus2*. 

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>Netwerkresources maken

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Een virtueel netwerk, subnet en openbaar IP-adres maken 
Deze resources worden gebruikt voor netwerkconnectiviteit met de virtuele machine en om verbinding met internet te maken. Maak het IP-adres in een beschikbaarheidszone, *2* in dit voorbeeld. In een latere stap maakt u de VM in dezelfde zone die is gebruikt voor het maken van het IP-adres.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)" -Sku Standard
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Een netwerkbeveiligingsgroep en een regel voor de netwerkbeveiligingsgroep maken 
De netwerkbeveiligingsgroep beveiligt de virtuele machine met binnenkomende en uitgaande regels. In dit geval is er een binnenkomende regel gemaakt voor poort 3389, waarmee binnenkomende verbindingen met een extern bureaublad worden toegestaan. We willen ook een regel maken voor poort 80, de poort voor binnenkomend webverkeer.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Een netwerkkaart voor de virtuele machine maken 
Maak een netwerkkaart met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) voor de virtuele machine. De netwerkkaart verbindt de virtuele machine met een subnet, netwerkbeveiligingsgroep en openbaar IP-adres.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Virtuele machine maken

Maak een virtuele-machineconfiguratie. Deze configuratie bevat de instellingen die worden gebruikt bij het implementeren van de virtuele machine, zoals een installatiekopie van de virtuele machine, de grootte en de verificatieconfiguratie. De grootte *Standard_DS1_v2* in dit voorbeeld wordt ondersteund in beschikbaarheidszones. In deze configuratie wordt ook de beschikbaarheidszone opgegeven die u hebt ingesteld bij het maken van het IP-adres. Als deze stap wordt uitgevoerd, wordt u gevraagd referenties op te geven. De waarden die u invoert, worden geconfigureerd als de gebruikersnaam en het wachtwoord voor de virtuele machine.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
```

Maak de virtuele machine met [New-AzVM](/powershell/module/az.compute/new-azvm).

```powershell
New-AzVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>Zone voor beheerde schijf bevestigen

U hebt de resource van het IP-adres van de virtuele machine gemaakt in dezelfde beschikbaarheidszone als de virtuele machine. De resource van de beheerde schijf voor de VM wordt in dezelfde beschikbaarheidszone gemaakt. U kunt dit controleren met [Get-AzDisk](/powershell/module/az.compute/get-azdisk):

```powershell
Get-AzDisk -ResourceGroupName myResourceGroup
```

In de uitvoer ziet u dat de beheerde schijf zich in dezelfde beschikbaarheidszone bevindt als de virtuele machine:

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een VM maakt in een beschikbaarheidszone. Meer informatie over [Beschik baarheid](../availability.md) voor virtuele Azure-machines.