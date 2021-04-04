---
title: Een SQL Server maken op een virtuele Windows-machine met Azure PowerShell | Microsoft Docs
description: In deze zelfstudie ziet u hoe u met Azure PowerShell een virtuele Windows-machine kunt maken waarop SQL Server 2017 wordt uitgevoerd.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 350df1aede076120fd14dc46da351835c894c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97356834"
---
# <a name="quickstart-create-sql-server-on-a-windows-virtual-machine-with-azure-powershell"></a>Quickstart: Een SQL Server maken op een virtuele Windows-machine met Azure PowerShell

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In deze quickstart gaat u een virtuele SQL Server-machine (VM) maken met Azure PowerShell.

> [!TIP]
> - Deze snelstartgids biedt een pad voor het snel inrichten van en verbinding maken met een SQL-VM. Zie de [Handleiding over de inrichting van SQL Server-VM's met Azure PowerShell](create-sql-vm-powershell.md) voor meer informatie over andere Azure PowerShell-opties voor het maken van virtuele SQL-machines.
> - Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](frequently-asked-questions-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Een Azure-abonnement nemen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


## <a name="get-azure-powershell"></a><a id="powershell"></a> Azure PowerShell downloaden

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>PowerShell configureren

1. Open PowerShell en zorg dat u toegang hebt tot uw Azure-account door de opdracht **Connect-AzAccount** uit te voeren.

   ```powershell
   Connect-AzAccount
   ```

1. Wanneer u het aanmeldvenster ziet, voert u uw referenties in. Gebruik hetzelfde e-mailadres en wachtwoord waarmee u zich aanmeldt bij Azure Portal.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

1. Definieer een variabele met een unieke naam voor de resourcegroep. In de andere opdrachten wordt deze naam gebruikt voor alle overige resourcenamen om de rest van de quickstart te vereenvoudigen.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Definieer de locatie van een Azure-doelregio voor alle VM-resources.

   ```powershell
   $Location = "East US"
   ```

1. Maak de resourcegroep.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Netwerkinstellingen configureren

1. Maak een virtueel netwerk, subnet en een openbaar IP-adres. Deze resources worden gebruikt voor netwerkconnectiviteit met de virtuele machine en om verbinding met internet te maken.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Maak een netwerkbeveiligingsgroep. Configureer regels om Extern bureaublad- (RDP) en SQL Server-verbindingen toe te staan.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Maak de netwerkinterface.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>De SQL-VM maken

1. Definieer uw referenties om u aan te melden bij de VM. De gebruikersnaam is 'azureadmin'. Wijzig \<password> voordat u de opdracht uitvoert.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Maak een configuratieobject voor de virtuele machine en maak vervolgens de VM. Met de volgende opdracht wordt een SQL Server 2017 Developer Edition-VM gemaakt in Windows Server 2016.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Het duurt enkele minuten om de VM te maken.

## <a name="register-with-sql-vm-rp"></a>Registreren met SQL-VM RP 

Voor integratie met de portal en voor de functies van de SQL-VM moet u zich registreren bij de [SQL IaaS-agentextensie](sql-agent-extension-manually-register-single-vm.md).

Als u de volledige functionaliteit wilt benutten, moet u zich in de volledige modus registreren bij de extensie. Aangezien de SQL Server-service hierdoor opnieuw wordt gestart, is de aanbevolen benadering om u te registreren in de LightWeight-modus en vervolgens tijdens een onderhoudsperiode een upgrade uit te voeren naar de volledige modus. 

Registreer uw SQL Server-VM eerst in de LightWeight-modus: 

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType PAYG -SqlManagementType LightWeight
```

Voer vervolgens tijdens een onderhoudsperiode een upgrade uit naar de Full-modus: 

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
      
# Register with SQL IaaS Agent extension in full mode
Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
```



## <a name="remote-desktop-into-the-vm"></a>Extern bureaublad op de VM

1. Gebruik de volgende opdracht om het openbare IP-adres voor de nieuwe VM op te halen.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Geef het geretourneerde IP-adres als opdrachtregelparameter door aan **mstsc** om een Extern bureaublad-sessie te starten op de nieuwe VM.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Wanneer u om referenties wordt gevraagd, voert u de referenties in voor een ander account. Voer de gebruikersnaam voorafgegaan door een backslash, (bijvoorbeeld `\azureadmin`), en het wachtwoord in dat u eerder in deze quickstart hebt ingesteld.

## <a name="connect-to-sql-server"></a>Verbinding maken met SQL Server

1. Nadat u bent aangemeld bij de Extern bureaublad-sessie, start u **SQL Server Management Studio 2017** vanuit het startmenu.

1. Laat de standaardwaarden in het dialoogvenster **Verbinding maken met server** staan. De servernaam is de naam van de VM. Verificatie is ingesteld op **Windows-verificatie**. Selecteer **Verbinden**.

U hebt nu lokaal verbinding met SQL Server. Als u extern verbinding wilt maken, moet u [connectiviteit handmatig of vanuit de Azure-portal configureren](ways-to-connect-to-sql.md).

## <a name="clean-up-resources"></a>Resources opschonen

Als het niet nodig is dat de VM continu wordt uitgevoerd, kunt u onnodige kosten voorkomen door de virtuele machine te stoppen wanneer deze niet in gebruik is. Met de volgende opdracht wordt de VM gestopt, maar blijft deze beschikbaar voor toekomstig gebruik.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

U kunt ook alle resources die aan de virtuele machine zijn gekoppeld, definitief verwijderen met de opdracht **Remove-AzResourceGroup**. Wees voorzichtig met het gebruik van deze opdracht, want hiermee verwijdert u ook de virtuele machine zelf definitief.

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u een virtuele SQL Server 2017-machine gemaakt met behulp van Azure PowerShell. Raadpleeg het volgende artikel voor meer informatie over hoe u uw gegevens migreert naar de nieuwe SQL-server.

> [!div class="nextstepaction"]
> [Een database migreren naar een SQL-VM](migrate-to-vm-from-sql-server.md)
