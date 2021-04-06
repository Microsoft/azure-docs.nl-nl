---
title: Meerdere IP-adressen voor Azure virtual machines-Power shell | Microsoft Docs
description: Meer informatie over het toewijzen van meerdere IP-adressen aan een virtuele machine met behulp van Power shell. | Resource Manager
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: allensu
ms.openlocfilehash: d86d4248b449ad3961a7798fd36a320eb6a74009
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217070"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Meerdere IP-adressen toewijzen aan virtuele machines met behulp van Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

In dit artikel wordt uitgelegd hoe u een virtuele machine (VM) maakt via het Azure Resource Manager-implementatie model met behulp van Power shell. Er kunnen geen meerdere IP-adressen worden toegewezen aan resources die zijn gemaakt via het klassieke implementatie model. Lees het artikel over [implementatie modellen begrijpen](../azure-resource-manager/management/deployment-models.md) voor meer informatie over Azure-implementatie modellen.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Een virtuele machine met meerdere IP-adressen maken

In de volgende stappen wordt uitgelegd hoe u een voor beeld van een virtuele machine met meerdere IP-adressen maakt, zoals beschreven in het scenario. Wijzig de variabelen waarden zoals vereist voor uw implementatie.

1. Open een Power shell-opdracht prompt en voltooi de resterende stappen in deze sectie binnen één Power shell-sessie. Als u Power shell nog niet hebt geïnstalleerd en geconfigureerd, voltooit u de stappen in het artikel [Azure PowerShell installeren en configureren](/powershell/azure/) .
2. Meld u aan bij uw account met de `Connect-AzAccount` opdracht.
3. Vervang *myResourceGroup* en *westus* door de naam en locatie van uw keuze. Een resourcegroep maken. Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Maak een virtueel netwerk (VNet) en subnet op dezelfde locatie als de resource groep:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Maak een netwerk beveiligings groep (NSG) en een regel. De NSG beveiligt de virtuele machine met behulp van binnenkomende en uitgaande regels. In dit geval is er een binnenkomende regel gemaakt voor poort 3389, waarmee binnenkomende verbindingen met een extern bureaublad worden toegestaan.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Definieer de primaire IP-configuratie voor de NIC. Wijzig 10.0.0.4 in een geldig adres in het subnet dat u hebt gemaakt, als u de eerder gedefinieerde waarde niet hebt gebruikt. Voordat u een statisch IP-adres toewijst, is het raadzaam om eerst te bevestigen dat het nog niet in gebruik is. Voer de opdracht in `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet` . Als het adres beschikbaar is, retourneert de uitvoer *waar*. Als deze niet beschikbaar is, retourneert de uitvoer *False* en een lijst met beschik bare adressen. 

    Vervang in de volgende opdrachten **door \<replace-with-your-unique-name> de unieke DNS-naam die u wilt gebruiken.** De naam moet uniek zijn voor alle open bare IP-adressen binnen een Azure-regio. Dit is een optionele para meter. U kunt het verwijderen als u alleen verbinding wilt maken met de virtuele machine met behulp van het open bare IP-adres.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Wanneer u meerdere IP-configuraties toewijst aan een NIC, moet één configuratie worden toegewezen als de *-Primary*.

    > [!NOTE]
    > Open bare IP-adressen hebben een nominale vergoeding. Lees de pagina met [prijzen voor IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses) voor meer informatie over de prijzen van IP-adressen. Er is een limiet voor het aantal open bare IP-adressen dat kan worden gebruikt in een abonnement. Lees voor meer informatie over de limieten het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

7. Definieer de secundaire IP-configuraties voor de NIC. U kunt indien nodig configuraties toevoegen of verwijderen. Aan elke IP-configuratie moet een privé-IP-adres zijn toegewezen. Aan elke configuratie kan eventueel één openbaar IP-adres worden toegewezen.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Maak de NIC en koppel de drie IP-configuraties hieraan:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Alle configuraties worden toegewezen aan één NIC in dit artikel, maar u kunt meerdere IP-configuraties toewijzen aan elke NIC die is gekoppeld aan de virtuele machine. Voor meer informatie over hoe u een VM met meerdere NIC's maakt, leest u het artikel [Een VM met meerdere NIC's maken](../virtual-machines/windows/multiple-nics.md).

9. Voer de volgende opdrachten in om de virtuele machine te maken:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Voeg de privé-IP-adressen toe aan het VM-besturings systeem door de stappen voor uw besturings systeem in de sectie [IP-adressen toevoegen aan een VM-besturings systeem](#os-config) van dit artikel uit te voeren. Voeg de open bare IP-adressen niet toe aan het besturings systeem.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>IP-adressen toevoegen aan een VM

U kunt persoonlijke en open bare IP-adressen toevoegen aan de Azure-netwerk interface door de volgende stappen uit te voeren. In de voor beelden in de volgende secties wordt ervan uitgegaan dat u al een virtuele machine hebt met de drie IP-configuraties die zijn beschreven in het [scenario](#scenario) in dit artikel, maar dit is niet vereist.

1. Open een Power shell-opdracht prompt en voltooi de resterende stappen in deze sectie binnen één Power shell-sessie. Als u Power shell nog niet hebt geïnstalleerd en geconfigureerd, voltooit u de stappen in het artikel [Azure PowerShell installeren en configureren](/powershell/azure/) .
2. Wijzig de "waarden" van de volgende $Variables in de naam van de NIC waaraan u het IP-adres wilt toevoegen en de resource groep en de locatie waar de NIC zich bevindt:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Als u de naam van de NIC die u wilt wijzigen niet weet, voert u de volgende opdrachten in en wijzigt u de waarden van de vorige variabelen:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Maak een variabele en stel deze in op de bestaande NIC door de volgende opdracht te typen:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. Wijzig in de volgende opdrachten *MyVNet* en *MySubnet* in de namen van het VNet en het subnet waarmee de NIC is verbonden. Voer de opdrachten in voor het ophalen van de VNet-en subnet-objecten waarmee de NIC is verbonden:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Als u de VNet-of subnetnaam waarmee de NIC is verbonden, niet weet, voert u de volgende opdracht in:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Zoek in de uitvoer naar tekst die vergelijkbaar is met de volgende voorbeeld uitvoer:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    In deze uitvoer is *MyVnet* het VNet en *MySubnet* is het subnet waarmee de NIC is verbonden.

5. Voer de stappen in een van de volgende secties uit, afhankelijk van uw vereisten:

   **Een privé-IP-adres toevoegen**

   Als u een privé-IP-adres aan een NIC wilt toevoegen, moet u een IP-configuratie maken. Met de volgende opdracht maakt u een configuratie met een statisch IP-adres van 10.0.0.7. Wanneer u een statisch IP-adres opgeeft, moet dit een ongebruikt adres voor het subnet zijn. Het is raadzaam om het adres eerst te testen om er zeker van te zijn dat het beschikbaar is door de opdracht in te voeren `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` . Als het IP-adres beschikbaar is, retourneert de uitvoer *waar*. Als deze niet beschikbaar is, retourneert de uitvoer *False* en een lijst met beschik bare adressen.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Maak zoveel configuraties als u nodig hebt, met behulp van unieke configuratie namen en privé-IP-adressen (voor configuraties met vaste IP-adressen).

   Voeg het privé-IP-adres toe aan het VM-besturings systeem door de stappen voor uw besturings systeem in de sectie [IP-adressen toevoegen aan een VM-besturings systeem](#os-config) van dit artikel uit te voeren.

   **Een openbaar IP-adres toevoegen**

   Een openbaar IP-adres wordt toegevoegd door een open bare IP-adres resource te koppelen aan een nieuwe IP-configuratie of een bestaande IP-configuratie. Voer de stappen in een van de volgende secties uit, zoals u dat nodig hebt.

   > [!NOTE]
   > Open bare IP-adressen hebben een nominale vergoeding. Lees de pagina met [prijzen voor IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses) voor meer informatie over de prijzen van IP-adressen. Er is een limiet voor het aantal open bare IP-adressen dat kan worden gebruikt in een abonnement. Lees voor meer informatie over de limieten het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
   >

   **De resource van het open bare IP-adres koppelen aan een nieuwe IP-configuratie**

   Wanneer u een openbaar IP-adres toevoegt aan een nieuwe IP-configuratie, moet u ook een persoonlijk IP-adres toevoegen, omdat alle IP-configuraties een privé-IP-adres moeten hebben. U kunt een bestaande resource met een openbaar IP-adres toevoegen of een nieuwe maken. Als u een nieuw account wilt maken, voert u de volgende opdracht in:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Voer de volgende opdracht in om een nieuwe IP-configuratie met een statisch privé-IP-adres en de bijbehorende open bare IP-adres bron *myPublicIp3* te maken:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **De resource van het open bare IP-adres koppelen aan een bestaande IP-configuratie**

   Een resource met een openbaar IP-adres kan alleen worden gekoppeld aan een IP-configuratie waaraan nog geen is gekoppeld. U kunt bepalen of een IP-configuratie een bijbehorend openbaar IP-adres heeft door de volgende opdracht in te voeren:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   De uitvoer ziet er ongeveer als volgt uit:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Omdat de kolom **PublicIpAddress** voor *IpConfig-3* leeg is, is er momenteel geen open bare IP-adres resource aan gekoppeld. U kunt een bestaand openbaar IP-adres toevoegen aan IpConfig-3 of de volgende opdracht invoeren om een resource te maken:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Voer de volgende opdracht in om de open bare IP-adres resource aan de bestaande IP-configuratie met de naam *IpConfig-3* te koppelen:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Stel de NIC in met de nieuwe IP-configuratie door de volgende opdracht in te voeren:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Bekijk de privé-IP-adressen en de open bare IP-adres resources die zijn toegewezen aan de NIC door de volgende opdracht in te voeren:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Voeg het privé-IP-adres toe aan het VM-besturings systeem door de stappen voor uw besturings systeem in de sectie [IP-adressen toevoegen aan een VM-besturings systeem](#os-config) van dit artikel uit te voeren. Voeg het open bare IP-adres niet toe aan het besturings systeem.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]