---
title: Een NAT-gateway maken - Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: In deze quickstart leert u hoe u een NAT-gateway kunt maken met Azure PowerShell.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 54ec676da3311473f86a47aae600c2ec29192bb2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935124"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Zelfstudie: Een NAT-gateway maken met Azure PowerShell

In deze zelfstudie wordt uitgelegd hoe u de Azure Virtual Network NAT-service gebruikt. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U kunt deze zelfstudie met behulp van Azure Cloud Shell voltooien of de opdrachten lokaal uitvoeren.  Als u Azure Cloud Shell niet hebt gebruikt, [meldt u zich hier aan](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroupNAT** gemaakt op de locatie **eastus2**:

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

Opties voor openbare IP voor de NAT-gateway zijn:

* **Openbare IP-adressen**
* **Voorvoegsels voor openbare IP**

Beide kunnen worden gebruikt met de NAT-gateway.

Om dit te illustreren, voegt u een openbaar IP-adres en een voorvoegsel voor openbare IP-adressen aan dit scenario toe.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Voor toegang tot het internet hebt u een of meer openbare IP-adressen nodig voor de NAT-gateway. Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om in **myResourceGroupNAT** een openbare IP-adresresource te maken met de naam **myPublicIP**. Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$publicIP**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel maken

Gebruik [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix) om in **myResourceGroupNAT** een resource voor het openbare IP-voorvoegsel te maken met de naam **myResourceGroupNAT**.  Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$publicIPPrefix**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gatewayresource maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service met de NAT-gatewayresource kunt maken en configureren:
  - Een openbare IP-adresgroep en een openbaar IP-voorvoegsel die moeten worden gebruikt voor uitgaande stromen die worden omgezet door de NAT-gatewayresource.
  - Wijzig de time-out voor inactiviteit van de standaardwaarde van 4 minuten naar 10 minuten.

Maak een globale Azure NAT-gateway met [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). Met deze opdracht maakt u een gatewayresource met de naam **myNATgateway** die het openbare IP-adres **myPublicIP** gebruikt en het openbare IP-voorvoegsel **myPublicIPprefix**. De time-out voor inactiviteit is ingesteld op 10 minuten.  Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$natGateway**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

Nu is de NAT-gateway functioneel. Het enige wat u nog hoeft te doen is te configureren welke subnetten van een virtueel netwerk er gebruik van moeten maken.

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Maak het virtuele netwerk en koppel het subnet aan de gateway.

Maak een virtueel netwerk met de naam **myVnet** met een subnet met de naam **mySubnet** met behulp van [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) in de **myResourceGroup** met behulp van [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). De IP-adresruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet binnen het virtuele netwerk is **192.168.0.0/24**.  Het resultaat van de opdrachten wordt voor later gebruik opgeslagen in variabelen met de namen **$subnet** en **$vnet**.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

Al het uitgaande verkeer naar Internetdoelen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.

## <a name="create-a-vm-to-use-the-nat-service"></a>Een VM maken voor het gebruik van de NAT-service

Nu gaat u een VM maken voor het gebruik van de NAT-service.  Deze VM heeft een openbaar IP-adres dat kan worden gebruikt als een openbaar IP-adres op exemplaarniveau om toegang te krijgen tot de virtuele machine.  De NAT-service neemt stroomrichting waar en vervangt de standaard internetbestemming in uw subnet. Het openbare IP-adres van de virtuele machine wordt niet gebruikt voor uitgaande verbindingen.

### <a name="create-public-ip-for-source-vm"></a>Een openbare IP voor de bron-VM maken

U maakt een openbare IP die gaat worden gebruikt voor toegang tot de VM.  Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om in **myResourceGroupNAT** een openbare IP-adresresource te maken met de naam **myPublicIPVM**.  Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$publicIpVM**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Een NSG maken en het SSH-eindpunt voor de virtuele machine weergeven

Standaard openbare IP-adressen zijn 'standaard beveiligd' en dus moet u een NSG maken om inkomende toegang voor SSH-toegang toe te staan. Gebruik [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) om een NSG-resource met de naam **myNSG** te maken. Gebruik [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) om een NSG-regel voor SSH-toegang te maken met de naam **ssh** in **myResourceGroupNAT**.  Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$nsg**.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>NIC maken voor VM

Maak met [New-AzureRmNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) een netwerkinterface met de naam **myNic**. Met deze opdracht worden het openbare IP-adres en de netwerkbeveiligingsgroep gekoppeld. Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$nic**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>VM maken

#### <a name="create-ssh-key-pair"></a>Een SSH-sleutelpaar maken

U hebt een SSH-sleutelpaar nodig om deze snelstart te volgen. Als u al een SSH-sleutelpaar hebt, kunt u deze stap overslaan.

Gebruik ssh-keygen om een SSH-sleutelpaar te maken.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Zie [SSH-sleutels gebruiken met Windows](../virtual-machines/linux/ssh-from-windows.md) voor gedetailleerde informatie over het maken van SSH-sleutelparen, waaronder het gebruik van PuTTy.

Als u het SSH-sleutelpaar maakt met behulp van de Cloud Shell, wordt het sleutelpaar opgeslagen in een containerinstallatiekopie. Dit [opslagaccount wordt automatisch gemaakt](../cloud-shell/persisting-shell-storage.md). Verwijder het opslagaccount, net als de bestandsshare in het account, pas nadat u de sleutels hebt opgehaald.

#### <a name="create-vm-configuration"></a>VM-configuratie maken

Voor het maken van een virtuele machine in PowerShell, maakt u een configuratie die instellingen bevat zoals de te gebruiken installatiekopie, grootte en verificatieopties. De configuratie wordt gebruikt om de virtuele machine te bouwen.

Definieer de SSH-referenties, besturingssysteeminformatie en grootte van de virtuele machine. In dit voorbeeld wordt de SSH-sleutel opgeslagen in ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combineer de configuratiedefinities om met [New-AzVM](/powershell/module/az.compute/new-azvm) een virtuele machine te maken met de naam **myVM** in **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Wacht tot de virtuele machine is voorbereid voor implementatie en ga vervolgens verder met de overige stappen.

## <a name="discover-the-ip-address-of-the-vm"></a>Het IP-adres van de VM ontdekken

Eerst moet het IP-adres van de VM die u hebt gemaakt, worden gedetecteerd. Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres van de virtuele machine op te halen. 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Kopieer de openbare IP en plak deze in Kladblok, zodat u deze kunt gebruiken om toegang te krijgen tot de VM.

### <a name="sign-in-to-vm"></a>Aanmelden bij VM

De SSH-aanmeldingsgegevens moeten worden opgeslagen in uw Cloud Shell van de vorige bewerking.  Open een [Azure Cloud Shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH op de virtuele machine uit te voeren.

```bash
ssh azureuser@<ip-address-destination>
```

U bent nu klaar om de NAT-service te gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep en alle gerelateerde resources daarin te verwijderen wanneer u ze niet meer nodig hebt.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een NAT-gateway gemaakt en een VM om de gateway te gebruiken. 

Controleer de metrische gegevens in Azure Monitor om uw NAT-service weer te geven. Stel problemen vast, zoals de resource-uitputting van de beschikbare SNAT-poorten.  Resource-uitputting van de SNAT-poorten kan worden verholpen door extra openbare IP-adresresources of openbare IP-voorvoegselresources of beide toe te voegen.


- Meer informatie over [Azure Virtual Network NAT](./nat-overview.md)
- Meer informatie over [NAT-gatewayresource](./nat-gateway-resource.md)
- Quickstart voor het implementeren van [NAT-gatewayresource met Azure CLI](./quickstart-create-nat-gateway-cli.md)
- Quickstart voor het implementeren van [NAT-gatewayresource met behulp van Azure PowerShell](./quickstart-create-nat-gateway-powershell.md)
- Quickstart voor het implementeren van [NAT-gatewayresource met de Azure-portal](./quickstart-create-nat-gateway-portal.md)
> [!div class="nextstepaction"]
