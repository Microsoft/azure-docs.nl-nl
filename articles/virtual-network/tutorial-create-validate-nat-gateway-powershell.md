---
title: Een NAT-gateway maken en testen - Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: In deze zelfstudie ziet u hoe u met behulp van Azure PowerShell een NAT-gateway maakt en de NAT-service test.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.openlocfilehash: d9f3fa67a0d3eee303ed307f1d64d30955348869
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222476"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Zelfstudie: Een NAT-gateway maken met behulp van Azure PowerShell en de NAT-service testen

In deze zelfstudie maakt u een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. Als u de NAT-gateway wilt testen, implementeert u een virtuele bron- en doelmachine. U gaat de NAT-gateway testen door uitgaande verbindingen te maken met een openbaar IP-adres. Deze verbindingen zijn afkomstig van de bron naar de virtuele doelmachine. In deze zelfstudie worden de bron en het doel in twee verschillende virtuele netwerken in dezelfde resourcegroep geïmplementeerd om het wat eenvoudiger te houden.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

U kunt deze zelfstudie met behulp van Azure Cloud Shell voltooien of de betreffende opdrachten lokaal uitvoeren.  Als u Azure Cloud Shell nooit hebt gebruikt, moet u zich nu [aanmelden](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroupNAT** gemaakt op de locatie **eastus2**:


```azurepowershell-interactive
$rgname = 'myResourceGroupNAT'
$loc = 'eastus2'

$rg = New-AzResourceGroup -Name $rgname -Location $loc

```

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Voor toegang tot het internet hebt u een of meer openbare IP-adressen nodig voor de NAT-gateway. Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) om in **myResourceGroupNAT** een openbare IP-adresresource te maken met de naam **myPublicIPsource**. Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$publicIPsource**.

```azurepowershell-interactive
$pipname = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pipname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -Sku $sku -Location $rg.Location

```

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel maken

 Gebruik [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix?view=latest) om in **myResourceGroupNAT** een resource voor het openbare IP-voorvoegsel te maken met de naam **myPublicIPprefixsource**.  Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$publicIPPrefixsource**.

```azurepowershell-interactive
$prefixname = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prefixname -ResourceGroupName $rg.ResourceGroupName -PrefixLength 31 -Location $rg.Location

```

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gatewayresource maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service met de NAT-gatewayresource kunt maken en configureren:
  - Een openbare IP-adresgroep en een openbaar IP-voorvoegsel die moeten worden gebruikt voor uitgaande stromen die worden vertaald door de NAT-gatewayresource.
  - Wijzig de time-out voor inactiviteit van de standaardwaarde van 4 minuten naar 10 minuten.

Maak een globale Azure NAT-gateway met [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). Met deze opdracht maakt u een gatewayresource met de naam **myNATgateway** die het openbare IP-adres **myPublicIPsource** gebruikt en het openbare IP-voorvoegsel **myPublicIPprefixsource**. De time-out voor inactiviteit is ingesteld op 10 minuten.  Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$natGateway**.

```azurepowershell-interactive
$sku = 'Standard'
$natname = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $natname -ResourceGroupName $rg.ResourceGroupName -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Sku $sku -IdleTimeoutInMinutes 10 -Location $rg.Location

  ```

Nu is de NAT-gateway functioneel. Het enige wat u nog hoeft te doen is te configureren welke subnetten van een virtueel netwerk er gebruik van moeten maken.

## <a name="prepare-the-source-for-outbound-traffic"></a>De bron voorbereiden voor uitgaand verkeer

U krijgt stapsgewijze instructies voor het instellen van een volledige testomgeving. U gaat een test instellen met behulp van opensource-hulpprogramma's om de NAT-gateway te controleren. We beginnen met de bron, die de NAT-gateway zal gebruiken die we eerder hebben gemaakt.

### <a name="configure-virtual-network-for-source"></a>Virtueel netwerk configureren voor bron

Maak het virtuele netwerk en koppel het subnet aan de gateway.

Maak een virtueel netwerk met de naam **myVnetsource** met een subnet met de naam **mySubnetsource** met behulp van [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in de **myResourceGroupNAT** met behulp van [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork?view=latest). De IP-adresruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet binnen het virtuele netwerk is **192.168.0.0/24**.  Het resultaat van de opdrachten wordt voor later gebruik opgeslagen in variabelen met de namen **$subnetsource** en **$vnetsource**.

```azurepowershell-interactive
$subnetname = 'mySubnetsource'
$subnetprefix = '192.168.0.0/24'
$vnetname = 'myVnetsource'
$vnetprefix = '192.168.0.0/16'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $subnetname -AddressPrefix $subnetprefix -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnetname -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vnetprefix -Subnet $subnetsource -Location $rg.Location

```

Al het uitgaande verkeer naar Internetdoelen maakt nu gebruik van de NAT-service.  Het is niet nodig om een UDR te configureren.

Voordat we de NAT-gateway kunnen testen, moeten we een bron-VM maken.  We wijzen een openbare IP-adresresource toe als een openbaar IP-adres op exemplaarniveau voor externe toegang tot deze VM. Dit adres wordt alleen gebruikt voor toegang tot de test.  We laten u zien hoe de NAT-service prioriteit krijgt boven andere uitgaande opties.

U kunt deze virtuele machine ook maken zonder een openbaar IP-adres en een andere virtuele machine maken om te gebruiken als jumpbox zonder openbare IP als oefening.

### <a name="create-public-ip-for-source-vm"></a>Een openbare IP voor de bron-VM maken

U maakt een openbare IP die gaat worden gebruikt voor toegang tot de VM.  Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) om in **myResourceGroupNAT** een openbare IP-adresresource te maken met de naam **myPublicIPVM**.  Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$publicIpsourceVM**.

```azurepowershell-interactive
$sku = 'Standard'
$pipvmname = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvmname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Een NSG maken en het SSH-eindpunt voor de virtuele machine weergeven

Omdat standaard openbare IP-adressen een 'standaardbeveiliging' vormen, moet u een NSG maken om inkomende toegang voor SSH-toegang toe te staan. De NAT-service kan de richting van een stroom detecteren. Deze NSG wordt niet gebruikt voor uitgaand verkeer wanneer de NAT-gateway is geconfigureerd in hetzelfde subnet. Gebruik [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) om een NSG-resource met de naam **myNSGsource** te maken. Gebruik [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) om een NSG-regel voor SSH-toegang te maken met de naam **ssh** in **myResourceGroupNAT**. Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$nsgsource**.

```azurepowershell-interactive
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule -Location $rg.Location

```

### <a name="create-nic-for-source-vm"></a>NIC maken voor bron-VM

Maak met [New-AzureRmNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) een netwerkinterface met de naam **myNicsource**. Met deze opdracht worden het openbare IP-adres en de netwerkbeveiligingsgroep gekoppeld. Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$nicsource**.

```azurepowershell-interactive
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-source-vm"></a>Een bron-VM maken

#### <a name="create-ssh-key-pair"></a>Een SSH-sleutelpaar maken

U hebt een SSH-sleutelpaar nodig om deze snelstart te volgen. Als u al een SSH-sleutelpaar hebt, kunt u deze stap overslaan.

Gebruik ssh-keygen om een SSH-sleutelpaar te maken.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048

```
Zie [SSH-sleutels gebruiken met Windows](../virtual-machines/linux/ssh-from-windows.md) voor gedetailleerde informatie over het maken van SSH-sleutelparen, waaronder het gebruik van PuTTy.

Als u het SSH-sleutelpaar maakt met behulp van de Cloud Shell, wordt het sleutelpaar opgeslagen in een containerinstallatiekopie. Dit [opslagaccount wordt automatisch gemaakt](../cloud-shell/persisting-shell-storage.md). Verwijder het opslagaccount, net als de bestandsshare in het account, pas nadat u de sleutels hebt opgehaald.

#### <a name="create-vm-configuration"></a>VM-configuratie maken

Voor het maken van een virtuele machine in PowerShell, maakt u een configuratie die instellingen bevat zoals de te gebruiken installatiekopie, grootte en verificatieopties. Vervolgens wordt de configuratie gebruikt om de virtuele machine te bouwen.

Definieer de SSH-referenties, besturingssysteeminformatie en grootte van de virtuele machine. In dit voorbeeld wordt de SSH-sleutel opgeslagen in ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combineer de configuratiedefinities om met [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) een virtuele machine te maken met de naam **myVMsource** in **myResourceGroupNAT**.

```azurepowershell-interactive
New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigsource -Location $rg.Location

```

Hoewel de opdracht direct een resultaat retourneert, kan het een paar minuten duren voordat de VM is geïmplementeerd.

## <a name="prepare-destination-for-outbound-traffic"></a>Doel voorbereiden voor uitgaand verkeer

We gaan nu een doel maken voor het uitgaande verkeer dat door de NAT-service is vertaald, zodat u het kunt testen.

### <a name="configure-virtual-network-for-destination"></a>Virtueel netwerk configureren voor bestemming

We moeten een virtueel netwerk maken voor de virtuele doelmachine.  Deze opdrachten zijn in grote lijnen hetzelfde als voor de bron-VM. Er zijn enkele kleine wijzigingen aangebracht om het doeleindpunt beschikbaar te maken.

Maak een virtueel netwerk met de naam **myVnetdestination** met een subnet met de naam **mySubnetdestination** met behulp van [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in de **myResourceGroupNAT** met behulp van [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork?view=latest). De IP-adresruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet binnen het virtuele netwerk is **192.168.0.0/24**.  Het resultaat van de opdrachten wordt voor later gebruik opgeslagen in variabelen met de namen **$subnetdestination** en **$vnetdestination**.

```azurepowershell-interactive
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vpfx -Subnet $subnetdestination -Location $rg.Location

```

### <a name="create-public-ip-for-destination-vm"></a>Openbare IP maken voor doel-VM

U maakt een openbare IP die gebruikt gaat worden voor toegang tot de doel-VM.  Gebruik [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress?view=latest) om in **myResourceGroupNAT** een openbare IP-adresresource te maken met de naam **myPublicIPdestinationVM**.  Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$publicIpdestinationVM**.

```azurepowershell-interactive
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $all -Sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Een NSG maken en het SSH- en HTTP-eindpunt voor de virtuele machine weergeven

Omdat standaard openbare IP-adressen een 'standaardbeveiliging' vormen, moet u een NSG maken om inkomende toegang voor SSH-toegang toe te staan. Gebruik [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) om een NSG-resource met de naam **myNSGdestination** te maken. Gebruik [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) om een NSG-regel voor SSH-toegang te maken met de naam **ssh**.  Gebruik [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) om een NSG-regel voor HTTP-toegang te maken met de naam **http**. Beide regels worden gemaakt in **myResourceGroupNAT**. Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$nsgdestination**.

```azurepowershell-interactive
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule,$httprule -Location $rg.Location

```

### <a name="create-nic-for-destination-vm"></a>NIC maken voor doel-VM

Maak met [New-AzureRmNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) een netwerkinterface met de naam **myNicdestination**. Met deze opdracht worden het openbare IP-adres en de netwerkbeveiligingsgroep gekoppeld. Het resultaat van deze opdracht wordt voor later gebruik opgeslagen in een variabele met de naam **$nicdestination**.

```azurepowershell-interactive
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-destination-vm"></a>Een doel-VM maken

#### <a name="create-vm-configuration"></a>VM-configuratie maken

Voor het maken van een virtuele machine in PowerShell, maakt u een configuratie die instellingen bevat zoals de te gebruiken installatiekopie, grootte en verificatieopties. Vervolgens wordt de configuratie gebruikt om de virtuele machine te bouwen.

Definieer de SSH-referenties, besturingssysteeminformatie en grootte van de virtuele machine. In dit voorbeeld wordt de SSH-sleutel opgeslagen in ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vmd = 'myVMdestination'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combineer de configuratiedefinities om met [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) een virtuele machine te maken met de naam **myVMdestination** in **myResourceGroupNAT**.

```azurepowershell-interactive

New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigdestination -Location $rg.Location

```

Hoewel de opdracht direct een resultaat retourneert, kan het een paar minuten duren voordat de VM is geïmplementeerd.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Een webserver voorbereiden en payload testen op de doel-VM

Eerst moet het IP-adres van de doel-VM worden gedetecteerd.  Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress?view=latest) om het openbare IP-adres van de virtuele machine op te halen. 

```azurepowershell-interactive
$pipname = 'myPublicIPdestinationVM'
  
$destip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$destip

``` 

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in Kladblok, zodat u het kunt gebruiken in de volgende stappen. Geef aan dat dit de virtuele doelmachine is.

### <a name="sign-in-to-destination-vm"></a>Aanmelden bij de doel-VM

De SSH-aanmeldingsgegevens moeten worden opgeslagen in uw Cloud Shell van de vorige bewerking.  Open een [Azure Cloud Shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine uit te voeren. 

```azurepowershell-interactive
ssh azureuser@$destip

```

Kopieer en plak de volgende opdrachten zodra u bent aangemeld.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Met deze opdrachten wordt uw virtuele machine bijgewerkt, nginx geïnstalleerd en een bestand van 100 KB gemaakt. Dit bestand wordt opgehaald van de bron-VM met behulp van de NAT-service.

Sluit de SSH-sessie met de doel-VM.

## <a name="prepare-test-on-source-vm"></a>Test voorbereiden op de bron-VM

Eerst moet het IP-adres van de bron-VM worden gedetecteerd.  Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress?view=latest) om het openbare IP-adres van de virtuele machine op te halen. 

```azurepowershell-interactive
$pipname = 'myPublicIPsourceVM'

$srcip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$srcip

``` 

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een Kladblok, zodat u het kunt gebruiken in de volgende stappen. Geef aan dat dit de virtuele bronmachine is.

### <a name="log-into-source-vm"></a>Aanmelden bij de bron-VM

De SSH-referenties worden zoals gezegd opgeslagen in Cloud Shell. Open een nieuw tabblad voor [Azure Cloud Shell](https://shell.azure.com) in uw browser.  Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine uit te voeren. 

```azurepowershell-interactive
ssh azureuser@$srcip

```

Kopieer en plak de volgende opdrachten om het testen van de NAT-service voor te bereiden.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Met deze opdrachten worden uw virtuele machine bijgewerkt, installeert u go, installeert u [hey](https://github.com/rakyll/hey) van GitHub en werkt u uw shell-omgeving bij.

U bent nu klaar om de NAT-service te testen.

## <a name="validate-nat-service"></a>NAT-service valideren

Terwijl u bent aangemeld bij de bron-VM, kunt u **curl** en **hey** om aanvragen te genereren voor het doel-IP-adres.

Gebruik curl om het bestand van 100 KB op te halen.  Vervang **\<ip-address-destination>** in het onderstaande voorbeeld met het doel-IP-adres dat u eerder hebt gekopieerd.  De parameter **--output** geeft aan dat het opgehaalde bestand wordt verwijderd.

```bash
curl http://<ip-address-destination>/100k --output /dev/null

```

U kunt ook een reeks aanvragen genereren met **hey**. Vervang nogmaals **\<ip-address-destination>** met het doel-IP-adres dat u eerder hebt gekopieerd.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k

```

Met deze opdracht worden 100 aanvragen gegenereerd, 10 gelijktijdig, met een time-out van 30 seconden. De TCP-verbinding wordt niet opnieuw gebruikt.  Elke aanvraag zal 100 KB ophalen.  Aan het einde van de uitvoering zal **hey** een aantal statistieken rapporteren over de juiste werking van de NAT-service.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=latest) gebruiken om de resourcegroep en alle gerelateerde resources daarin te verwijderen wanneer u ze niet meer nodig hebt.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name $rg.ResourceGroupName

```

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een NAT-gateway gemaakt, een bron- en doel-VM gemaakt en vervolgens de NAT-gateway getest.

Controleer de metrische gegevens in Azure Monitor om uw NAT-service weer te geven. Problemen vaststellen, zoals de resource-uitputting van de beschikbare SNAT-poorten.  Resource-uitputting van de SNAT-poorten kan gemakkelijk worden verholpen door extra openbare IP-adresresources of openbare IP-voorvoegselresources of beide toe te voegen.

- Meer informatie over [Virtual Network NAT](./nat-overview.md)
- Meer informatie over [NAT-gatewayresource](./nat-gateway-resource.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met behulp van Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met Azure Portal](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]