---
title: Hand leiding voor het inrichten van Azure PowerShell om SQL Server in te richten op Azure VM
description: Bevat stappen en Power shell-opdrachten voor het maken van een Azure-VM met SQL Server galerie installatie kopieën van virtuele machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a3f51a07b274320d1cd9f12b33703d8ec7f21f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359656"
---
# <a name="how-to-use-azure-powershell-to-provision-sql-server-on-azure-virtual-machines"></a>Azure PowerShell gebruiken om SQL Server in te richten op Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Deze hand leiding bevat opties voor het inrichten van SQL Server op Azure Virtual Machines (Vm's) voor het gebruik van Power shell. Voor een gestroomlijnd Azure PowerShell-voor beeld dat afhankelijk is van standaard waarden, raadpleegt u de [virtuele machine van SQL Azure PowerShell Quick](sql-vm-create-powershell-quickstart.md)start.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Uw abonnement configureren

1. Open PowerShell en zorg dat u toegang hebt tot uw Azure-account door de opdracht **Connect-AzAccount** uit te voeren.

   ```powershell
   Connect-AzAccount
   ```

1. Voer uw referenties in wanneer dit wordt gevraagd. Gebruik hetzelfde e-mailadres en wachtwoord waarmee u zich aanmeldt bij Azure Portal.

## <a name="define-image-variables"></a>Afbeeldings variabelen definiëren

Als u waarden opnieuw wilt gebruiken en het maken van scripts wilt vereenvoudigen, kunt u beginnen met het definiëren van een aantal variabelen. Wijzig de parameter waarden zoals u wilt, maar houd rekening met de naamgevings beperkingen met betrekking tot naam lengten en speciale tekens bij het wijzigen van de gegeven waarden.

### <a name="location-and-resource-group"></a>Locatie en resource groep

Definieer het gegevens gebied en de resource groep waar u de andere VM-resources wilt maken.

Wijzig de gewenste waarden en voer vervolgens deze cmdlets uit om deze variabelen te initialiseren.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Opslag eigenschappen

Definieer het opslag account en het type opslag dat moet worden gebruikt door de virtuele machine.

Wijzig indien gewenst en voer de volgende cmdlet uit om deze variabelen te initialiseren. We raden aan om [Premium ssd's](../../../virtual-machines/disks-types.md#premium-ssd) te gebruiken voor productie werkbelastingen.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Netwerk eigenschappen

Definieer de eigenschappen die door het netwerk in de virtuele machine moeten worden gebruikt. 

- Netwerkinterface
- TCP/IP-toewijzings methode
- Naam van virtueel netwerk
- Naam van virtueel subnet
- Bereik van IP-adressen voor het virtuele netwerk
- Bereik van IP-adressen voor het subnet
- Label voor open bare domein naam

Wijzig indien gewenst en voer deze cmdlet uit om deze variabelen te initialiseren.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Eigenschappen van de virtuele machine

Definieer de volgende eigenschappen:

- Naam van de virtuele machine
- Computernaam
- Grootte van de virtuele machine
- Schijf naam van het besturings systeem voor de virtuele machine

Wijzig indien gewenst en voer deze cmdlet uit om deze variabelen te initialiseren.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Een SQL Server installatie kopie kiezen

Gebruik de volgende variabelen om de SQL Server installatie kopie te definiëren die moet worden gebruikt voor de virtuele machine. 

1. Eerst een lijst van alle SQL Server installatie kopie met de `Get-AzVMImageOffer` opdracht. Met deze opdracht worden de huidige installatie kopieën weer gegeven die beschikbaar zijn in de Azure Portal en ook oudere installatie kopieën die alleen met Power shell kunnen worden geïnstalleerd:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Voor deze zelf studie gebruikt u de volgende variabelen om SQL Server 2017 op te geven in Windows Server 2016.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Vervolgens vermeldt u de beschik bare edities voor uw aanbieding.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Voor deze zelf studie gebruikt u de SQL Server 2017 Developer Edition (**SQLDEV**). De Developer Edition is gratis gelicentieerd voor testen en ontwikkeling en u betaalt alleen voor de kosten van het uitvoeren van de virtuele machine.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Met het Resource Manager-implementatie model is het eerste object dat u maakt de resource groep. Gebruik de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) om een Azure-resource groep en de bijbehorende resources te maken. Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam en locatie van de resource groep.

Voer deze cmdlet uit om uw nieuwe resource groep te maken.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Een opslagaccount maken

Voor de virtuele machine zijn opslag resources vereist voor de schijf met het besturings systeem en voor de SQL Server gegevens en logboek bestanden. Voor eenvoud maakt u één schijf voor beide. U kunt later extra schijven toevoegen met behulp van de cmdlet [add-Azure Disk](/powershell/module/servicemanagement/azure.service/add-azuredisk) om uw SQL Server-gegevens en-logboek bestanden op toegewezen schijven te plaatsen. Gebruik de cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) om een standaard-opslag account te maken in de nieuwe resource groep. Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam van het opslag account, de naam van de opslag-SKU en de locatie.

Voer deze cmdlet uit om uw nieuwe opslag account te maken.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Het maken van het opslag account kan een paar minuten duren.

## <a name="create-network-resources"></a>Netwerkbronnen maken

De virtuele machine vereist een aantal netwerk bronnen voor de netwerk verbinding.

* Voor elke virtuele machine is een virtueel netwerk vereist.
* Voor een virtueel netwerk moet ten minste één subnet zijn gedefinieerd.
* U moet een netwerk interface definiëren met een openbaar of een privé-IP-adres.

### <a name="create-a-virtual-network-subnet-configuration"></a>Een subnet-configuratie voor een virtueel netwerk maken

Begin met het maken van een subnet-configuratie voor uw virtuele netwerk. Voor deze zelf studie maakt u een standaard subnet met behulp van de cmdlet [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) . Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam en het adres voorvoegsel van het subnet.

> [!NOTE]
> U kunt aanvullende eigenschappen van de configuratie van het subnet van het virtuele netwerk definiëren met deze cmdlet, maar dit valt buiten het bereik van deze zelf studie.

Voer deze cmdlet uit om de configuratie van uw virtuele subnet te maken.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak vervolgens het virtuele netwerk in de nieuwe resource groep met behulp van de cmdlet [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) . Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam, locatie en adres voorvoegsel. Gebruik de configuratie van het subnet dat u in de vorige stap hebt gedefinieerd.

Voer deze cmdlet uit om het virtuele netwerk te maken.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Het open bare IP-adres maken

Nu het virtuele netwerk is gedefinieerd, moet u een IP-adres configureren voor de verbinding met de virtuele machine. Voor deze zelf studie maakt u een openbaar IP-adres met dynamische IP-adres sering voor de ondersteuning van Internet connectiviteit. Gebruik de cmdlet [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) om het open bare IP-adres in uw nieuwe resource groep te maken. Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam, locatie, toewijzings methode en label voor de DNS-domein naam.

> [!NOTE]
> U kunt met behulp van deze cmdlet aanvullende eigenschappen van het open bare IP-adres definiëren, maar dit valt buiten het bereik van deze eerste zelf studie. U kunt ook een persoonlijk adres of een adres met een statisch adres maken, maar dit is ook buiten het bereik van deze zelf studie.

Voer deze cmdlet uit om uw open bare IP-adres te maken.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>De netwerk beveiligings groep maken

Als u de virtuele machine en het SQL Server verkeer wilt beveiligen, maakt u een netwerk beveiligings groep.

1. Maak eerst een regel voor de netwerk beveiligings groep voor extern bureau blad (RDP) om RDP-verbindingen toe te staan.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Configureer een regel voor de netwerk beveiligings groep waarmee verkeer op TCP-poort 1433 wordt toegestaan. Hierdoor kunnen verbindingen via internet worden SQL Server.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Maak de netwerk beveiligings groep.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>De netwerk interface maken

Nu bent u klaar om de netwerk interface voor uw virtuele machine te maken. Gebruik de cmdlet [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) om de netwerk interface in de nieuwe resource groep te maken. Geef de naam, locatie, het subnet en het open bare IP-adres op dat u eerder hebt gedefinieerd.

Voer deze cmdlet uit om uw netwerk interface te maken.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Een VM-object configureren

Nu de opslag-en netwerk resources zijn gedefinieerd, bent u klaar om reken resources te definiëren voor de virtuele machine.

- Geef de grootte van de virtuele machine en de verschillende eigenschappen van het besturings systeem op.
- Geef de netwerk interface op die u eerder hebt gemaakt.
- Definieer de Blob-opslag.
- Geef de schijf van het besturings systeem op.

### <a name="create-the-vm-object"></a>Het VM-object maken

Begin door de grootte van de virtuele machine op te geven. Geef voor deze zelf studie een DS13 op. Gebruik de cmdlet [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) om een configureerbaar virtuele-machine object te maken. Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam en grootte.

Voer deze cmdlet uit om het virtuele-machine object te maken.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Een referentie object maken om de naam en het wacht woord op te slaan voor de referenties van de lokale beheerder

Voordat u de eigenschappen van het besturings systeem voor de virtuele machine kunt instellen, moet u de referenties voor het lokale beheerders account opgeven als een veilige teken reeks. U kunt dit doen met behulp van de cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) .

Voer de volgende cmdlet uit. U moet de naam en het wacht woord van de lokale beheerder van de virtuele machine in het venster voor de Power shell-referentie aanvraag typen.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>De eigenschappen van het besturings systeem voor de virtuele machine instellen

Nu bent u klaar om de eigenschappen van het besturings systeem van de virtuele machine in te stellen met de cmdlet [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) .

- Stel het type besturings systeem in als Windows.
- Vereisen dat de agent van de [virtuele machine](../../../virtual-machines/extensions/agent-windows.md) wordt geïnstalleerd.
- Hiermee geeft u op dat de cmdlet automatisch bijwerken inschakelt.
- Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam van de virtuele machine, de computer naam en de referentie.

Voer deze cmdlet uit om de eigenschappen van het besturings systeem voor de virtuele machine in te stellen.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>De netwerk interface toevoegen aan de virtuele machine

Gebruik vervolgens de cmdlet [add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) om de netwerk interface toe te voegen met behulp van de variabele die u eerder hebt gedefinieerd.

Voer deze cmdlet uit om de netwerk interface voor uw virtuele machine in te stellen.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Stel de Blob-opslag locatie in voor de schijf die moet worden gebruikt door de virtuele machine

Stel vervolgens de Blob-opslag locatie voor de schijf van de virtuele machine in met de variabelen die u eerder hebt gedefinieerd.

Voer deze cmdlet uit om de Blob-opslag locatie in te stellen.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>De eigenschappen van de besturingssysteem schijf voor de virtuele machine instellen

Stel vervolgens de eigenschappen van de besturingssysteem schijf voor de virtuele machine in met de cmdlet [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) . 

- Geef op dat het besturings systeem voor de virtuele machine afkomstig is van een installatie kopie.
- Stel caching in op alleen-lezen (omdat SQL Server op dezelfde schijf wordt geïnstalleerd).
- Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam van de virtuele machine en van het besturings systeem.

Voer deze cmdlet uit om de eigenschappen van de besturingssysteem schijf voor de virtuele machine in te stellen.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>De platform installatie kopie voor de virtuele machine opgeven

De laatste configuratie stap is het opgeven van de platform installatie kopie voor de virtuele machine. Voor deze zelf studie gebruikt u de nieuwste SQL Server 2016 CTP-installatie kopie. Gebruik de cmdlet [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) voor het gebruik van deze installatie kopie met de variabelen die u eerder hebt gedefinieerd.

Voer deze cmdlet uit om de platform installatie kopie voor de virtuele machine op te geven.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>De SQL-VM maken

Nu u de configuratie stappen hebt voltooid, bent u klaar om de virtuele machine te maken. Gebruik de cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm) om de virtuele machine te maken met de variabelen die u hebt gedefinieerd.

> [!TIP]
> Het opstarten van de virtuele machine kan enkele minuten duren.

Voer deze cmdlet uit om uw virtuele machine te maken.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

De virtuele machine wordt gemaakt.

> [!NOTE]
> Als er een fout optreedt in de diagnostische gegevens over opstarten, kunt u deze negeren. Er wordt een standaard-opslag account voor diagnostische gegevens over opstarten gemaakt, omdat het opgegeven opslag account voor de schijf van de virtuele machine een Premium-opslag account is.

## <a name="install-the-sql-iaas-agent"></a>SQL IaaS-agent installeren

SQL Server virtuele machines ondersteunen geautomatiseerde beheer functies met de [uitbrei ding SQL Server IaaS agent](sql-server-iaas-agent-extension-automate-management.md). Als u uw SQL Server wilt registreren met de uitbrei ding, voert u de opdracht [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) uit nadat de virtuele machine is gemaakt. Geef het licentie type voor uw SQL Server virtuele machine op en kies een van de betalen per gebruik-of uw eigen licentie via de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Zie [licentie model](licensing-model-azure-hybrid-benefit-ahb-change.md)voor meer informatie over licentie verlening. 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```

Er zijn drie manieren om u te registreren bij de extensie: 
- [Automatisch voor alle huidige en toekomstige Vm's in een abonnement](sql-agent-extension-automatic-registration-all-vms.md)
- [Hand matig voor één VM](sql-agent-extension-manually-register-single-vm.md)
- [Hand matig voor meerdere Vm's tegelijk](sql-agent-extension-manually-register-vms-bulk.md)


## <a name="stop-or-remove-a-vm"></a>Een virtuele machine stoppen of verwijderen

Als het niet nodig is dat de VM continu wordt uitgevoerd, kunt u onnodige kosten voorkomen door de virtuele machine te stoppen wanneer deze niet in gebruik is. Met de volgende opdracht wordt de VM gestopt, maar blijft deze beschikbaar voor toekomstig gebruik.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

U kunt ook alle resources die aan de virtuele machine zijn gekoppeld, definitief verwijderen met de opdracht **Remove-AzResourceGroup**. Wees voorzichtig met het gebruik van deze opdracht, want hiermee verwijdert u ook de virtuele machine zelf definitief.

## <a name="example-script"></a>Voorbeeldscript

Het volgende script bevat het volledige Power shell-script voor deze zelf studie. Hierbij wordt ervan uitgegaan dat u het Azure-abonnement al hebt ingesteld voor gebruik met de opdrachten **Connect-AzAccount** en **Select-AzSubscription** .

```powershell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>Volgende stappen

Nadat de virtuele machine is gemaakt, kunt u het volgende doen:

- Verbinding maken met de virtuele machine met behulp van RDP
- Configureer SQL Server instellingen in de portal voor uw VM, waaronder:
   - [Opslag instellingen](storage-configuration.md) 
   - [Geautomatiseerde beheer taken](sql-server-iaas-agent-extension-automate-management.md)
- [Connectiviteit configureren](ways-to-connect-to-sql.md)
- Clients en toepassingen verbinden met het nieuwe SQL Server-exemplaar