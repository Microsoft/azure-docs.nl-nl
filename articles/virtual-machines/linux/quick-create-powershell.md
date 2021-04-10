---
title: 'Quickstart: Een virtuele Linux-machine maken met Azure PowerShell'
description: In deze snelstart leert u hoe u Azure PowerShell gebruikt om een virtuele Linux-machine te maken
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/31/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2cef611fe79ca04303840076b09b4cf6344b7e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102616227"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Quickstart: Een virtuele Linux-machine maken in Azure met PowerShell

De Azure PowerShell-module wordt gebruikt voor het maken en beheren van Azure-resources vanaf de PowerShell-opdrachtregel of in scripts. In deze snelstart wordt beschreven hoe u de Azure PowerShell-module gebruikt voor het implementeren van een virtuele Linux-machine (VM) in Azure. In deze quickstart wordt de Marketplace-installatiekopie voor Ubuntu 18.04 LTS van Canonical gebruikt. Wanneer u uw virtuele machine in actie wilt zien, voert u ook SSH voor de virtuele machine uit en installeert u de NGINX-webserver.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="create-ssh-key-pair"></a>Een SSH-sleutelpaar maken

Gebruik[ssh-keygen](https://www.ssh.com/ssh/keygen/) om een SSH-sleutelpaar te maken. Als u al een SSH-sleutelpaar hebt, kunt u deze stap overslaan.


```azurepowershell-interactive
ssh-keygen -t rsa -b 4096
```

U wordt gevraagd om een bestandsnaam op te geven voor het sleutelpaar of u kunt op **Enter** drukken om de standaardlocatie van `/home/<username>/.ssh/id_rsa` te gebruiken. U kunt desgewenst ook een wachtwoord voor de sleutels maken.

Zie [SSH-sleutels gebruiken met Windows](ssh-from-windows.md) voor gedetailleerde informatie over het maken van SSH-sleutelparen.

Als u het SSH-sleutelpaar maakt met behulp van Cloud Shell, wordt dit opgeslagen in een [opslagaccount dat automatisch wordt gemaakt door Cloud Shell](../../cloud-shell/persisting-shell-storage.md). Verwijder het opslagaccount of de bestandsshare die het bevat niet voordat u uw sleutels hebt opgehaald. Anders verliest u toegang tot de virtuele machine. 

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Virtuele netwerkresources aanmaken

Maak een virtueel netwerk, subnet en een openbaar IP-adres. Deze resources worden gebruikt voor netwerkconnectiviteit met de virtuele machine en om deze te verbinden met internet:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

Maak een Azure-netwerkbeveiligingsgroep en verkeersregel. De netwerkbeveiligingsgroep beveiligt de virtuele machine met binnenkomende en uitgaande regels. In het volgende voorbeeld wordt een binnenkomende regel gemaakt voor TCP-poort 22, die SSH-verbindingen toestaat. Er wordt ook een binnenkomende regel voor TCP-poort 80 gemaakt om binnenkomend verkeer toe te staan.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Een virtuele netwerkinterfacekaart (NIC) maken met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). De virtuele NIC verbindt de virtuele machine met een subnet, netwerkbeveiligingsgroep en openbaar IP-adres.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Voor het maken van een virtuele machine in PowerShell, maakt u een configuratie die instellingen bevat zoals de te gebruiken installatiekopie, grootte en verificatieopties. Vervolgens wordt de configuratie gebruikt om de virtuele machine te bouwen.

Definieer de SSH-referenties, besturingssysteeminformatie en grootte van de virtuele machine. In dit voorbeeld is de SSH-sleutel opgeslagen in `~/.ssh/id_rsa.pub`. 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "18.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

Combineer nu de vorige configuratiedefinities met [New-AzVM](/powershell/module/az.compute/new-azvm):

```azurepowershell-interactive
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd. Wanneer de implementatie is voltooid, gaat u verder naar de volgende sectie.

## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

Maak een SSH-verbinding met de virtuele machine met behulp van het openbare IP-adres. Gebruik de cmdlet [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres van de virtuele machine te bekijken:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Gebruik dezelfde shell die u hebt gebruikt om uw SSH-sleutelpaar te maken en plak de volgende opdracht in de shell om een SSH-sessie te maken. Vervang *10.111.12.123* door het IP-adres van uw virtuele machine.

```bash
ssh azureuser@10.111.12.123
```

Als u hierom wordt gevraagd, is de gebruikersnaam voor het aanmelden *azureuser*. Als een wachtwoordzin met uw SSH-sleutels wordt gebruikt, moet u deze invoeren wanneer u daarom wordt gevraagd.


## <a name="install-nginx"></a>NGINX installeren

Als u uw VM in actie wilt zien, installeert u de NGINX-webserver. Werk vanuit uw SSH-sessie de pakketbronnen bij en installeer het meest recente NGINX-pakket.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Wanneer u klaar bent, typt u `exit` om de SSH-sessie te verlaten.


## <a name="view-the-web-server-in-action"></a>De webserver in actie zien

Gebruik een webbrowser naar keuze om de standaard NGINX-welkomstpagina weer te geven. Gebruik het openbare IP-adres van uw VM als het webadres. Het openbare IP-adres kan worden gevonden op de overzichtspagina van de virtuele machine of als onderdeel van de SSH-verbindingsreeks die u eerder hebt gebruikt.

![Standaard welkomstpagina van NGINX](./media/quick-create-cli/nginix-welcome-page.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een eenvoudige virtuele machine geïmplementeerd, een netwerkbeveiligingsgroep en -regel gemaakt en een eenvoudige webserver geïnstalleerd. Voor meer informatie over virtuele machines in Azure, gaat u verder met de zelfstudie voor virtuele Linux-machines.

> [!div class="nextstepaction"]
> [Zelfstudies over virtuele Linux-machines](./tutorial-manage-vm.md)
