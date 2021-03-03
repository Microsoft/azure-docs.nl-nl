---
title: Zelfstudie - Virtuele Azure-netwerken maken en beheren voor virtuele Windows-machines
description: In deze zelfstudie leert u hoe u Azure PowerShell gebruikt voor het maken en beheren van virtuele Azure-netwerken voor virtuele Windows-machines
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: networking
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/04/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9552e9c6deaabc9c847a8fbd26b1246b073dcc1b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673275"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-windows-virtual-machines-with-azure-powershell"></a>Zelfstudie: Virtuele Azure-netwerken voor virtuele Windows-machines maken en beheren met Azure PowerShell

Virtuele Azure-machines maken gebruik van Azure-netwerken voor interne en externe communicatie. In deze zelfstudie wordt uitgelegd hoe u twee virtuele machines implementeert en Azure-netwerken configureert voor deze virtuele machines. In de voorbeelden in deze zelfstudie wordt ervan uitgegaan dat de virtuele machines een webtoepassing met een databaseback-end hosten, maar er wordt geen toepassing geïmplementeerd in de zelfstudie. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel netwerk en een subnet maken
> * Een openbaar IP-adres maken
> * Een front-end virtuele machine maken
> * Netwerkverkeer beveiligen
> * Een back-end virtuele machine maken


## <a name="vm-networking-overview"></a>Overzicht van VM-netwerken

Virtuele Azure-netwerken maken beveiligde netwerkverbindingen mogelijk tussen virtuele machines, internet en andere Azure-services zoals Azure SQL Database. Virtuele netwerken zijn onderverdeeld in logische segmenten die subnetten worden genoemd. Subnetten worden gebruikt om de netwerkstroom te beheersen, en als een beveiligingsgrens. Wanneer een virtuele machine wordt geïmplementeerd, omvat deze doorgaans een virtuele netwerkinterface, die is gekoppeld aan een subnet.

Tijdens het volgen van deze zelfstudie worden de volgende resources gemaakt:

![Virtueel netwerk met twee subnetten](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet*: het virtuele netwerk dat de virtuele machines gebruiken om met elkaar en met internet te communiceren.
- *myFrontendSubnet*: het subnet in *myVNet* dat door de front-endresources wordt gebruikt.
- *myPublicIPAddress*: het openbare IP-adres dat wordt gebruikt voor toegang tot *myFrontendVM* via internet.
- *myFrontendNic*: de netwerkinterface die wordt gebruikt door *myFrontendVM* om te communiceren met *myBackendVM*.
- *myFrontendVM*: de VM die wordt gebruikt voor communicatie tussen internet en *myBackendVM*.
- *myBackendNSG*: de netwerkbeveiligingsgroep waardoor de communicatie tussen de *myFrontendVM* en *myBackendVM* wordt bepaald.
- *myBackendSubnet*: het subnet dat is gekoppeld aan *myBackendNSG* en door de back-endresources wordt gebruikt.
- *myBackendNic*: de netwerkinterface die wordt gebruikt door *myBackendVM* om te communiceren met *myFrontendVM*.
- *myBackendVM*: de virtuele machine die gebruikmaakt van poort 1433 om te communiceren met *myFrontendVM*.


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.


## <a name="create-subnet"></a>Subnet maken 

Voor deze zelfstudie wordt één virtueel netwerk met twee subnetten gemaakt. Een front-endsubnet voor het hosten van een webtoepassing en een back-endsubnet voor het hosten van een databaseserver.

Voordat u een virtueel netwerk kunt maken, moet u eerst een resourcegroep maken met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myRGNetwork* gemaakt op de locatie *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Maak een subnetconfiguratie genaamd *myFrontendSubnet* met [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

En maak een subnetconfiguratie genaamd *myBackendSubnet*:

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
```

## <a name="create-virtual-network"></a>Virtueel netwerk maken

Maak een VNET genaamd *myVNet* met *myFrontendSubnet* en *myBackendSubnet* met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet, $backendSubnet
```

Op dit moment is er een netwerk gemaakt en gesegmenteerd in twee subnetten, één voor de front-endservices en een andere voor back-endservices. In de volgende sectie worden de virtuele machines gemaakt en verbonden met deze subnetten.

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Met een openbaar IP-adres kunnen Azure-resources toegankelijk zijn via internet. De toewijzingsmethode van het openbare IP-adres kan worden geconfigureerd als dynamisch of statisch. Een openbaar IP-adres wordt standaard dynamisch toegewezen. Dynamische IP-adressen worden vrijgegeven wanneer de toewijzing van een virtuele machine ongedaan wordt gemaakt. Dit gedrag zorgt ervoor dat het IP-adres wijzigt tijdens een bewerking waarbij de toewijzing van een virtuele machine ongedaan wordt gemaakt.

De toewijzingsmethode kan worden ingesteld op statisch. Dit zorgt ervoor dat het IP-adres aan een virtuele machine toegewezen blijft, zelfs bij een status waarin de toewijzing ongedaan is gemaakt. Wanneer u een statisch toegewezen IP-adres gebruikt, kan het IP-adres zelf niet worden opgegeven. Dit wordt toegewezen uit een groep met beschikbare adressen.

Maak een openbaar IP-adres met de naam *myPublicIPAddress* met behulp van [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

```azurepowershell-interactive
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Dynamic `
  -Name myPublicIPAddress
```

U kunt de parameter - AllocationMethod wijzigen in `Static` om een statisch openbaar IP-adres toe te wijzen.

## <a name="create-a-front-end-vm"></a>Een front-end virtuele machine maken

Voor communicatie in een virtueel netwerk heeft een VM een virtuele netwerkinterface (NIC) nodig. Maak een NIC met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

```azurepowershell-interactive
$frontendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontend `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Stel met [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) de gebruikersnaam en het wachtwoord in die nodig zijn voor het beheerdersaccount op de VM. U gebruikt deze referenties om verbinding te maken met de VM met extra stappen:

```azurepowershell-interactive
$cred = Get-Credential
```

Maak de VM's met [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myFrontend `
   -PublicIpAddressName myPublicIPAddress `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -Size Standard_D1 `
   -SubnetName myFrontendSubnet `
   -VirtualNetworkName myVNet
```

## <a name="secure-network-traffic"></a>Netwerkverkeer beveiligen

Een netwerkbeveiligingsgroep (Network Security Group, NSG) bevat een lijst met beveiligingsregels waarmee netwerkverkeer naar resources die zijn verbonden met virtuele Azure-netwerken (VNet) wordt toegestaan of geweigerd. Netwerkbeveiligingsgroepen kunnen worden gekoppeld aan subnetten of afzonderlijke netwerkinterfaces. Wanneer een netwerkbeveiligingsgroep is gekoppeld aan een netwerkinterface, is deze alleen van toepassing op de gekoppelde virtuele machine. Wanneer een NSG is gekoppeld aan een subnet, zijn de regels van toepassing op alle resources die zijn verbonden met het subnet.

### <a name="network-security-group-rules"></a>Regels voor netwerkbeveiligingsgroepen

Met regels voor netwerkbeveiligingsgroepen worden netwerkpoorten gedefinieerd waarover verkeer wordt toegestaan of geweigerd. De regels kunnen bron- en doel-IP-adresbereiken bevatten, zodat verkeer wordt geregeld tussen specifieke systemen of subnetten. Regels voor netwerkbeveiligingsgroepen bevatten ook een prioriteit (tussen 1- en 4096). Regels worden in volgorde van prioriteit gecontroleerd. Een regel met een prioriteit van 100 wordt geëvalueerd vóór een regel met een prioriteit van 200.

Alle NSG's bevatten een set met standaardregels. De standaardregels kunnen niet worden verwijderd, maar omdat ze de laagste prioriteit hebben, kunnen ze worden overschreven door de regels die u maakt.

- **Virtueel netwerk**: verkeer dat afkomstig is van en eindigt in een virtueel netwerk wordt toegestaan in zowel binnenkomende als uitgaande richting.
- **Internet**: uitgaand verkeer is toegestaan, maar binnenkomend verkeer wordt geblokkeerd.
- **Load balancer:** : toestaan dat de load balancer van Azure de status van uw VM's en rolexemplaren controleert. Als u geen set met taakverdeling gebruikt, kunt u deze regel onderdrukken.

### <a name="create-network-security-groups"></a>Netwerkbeveiligingsgroepen maken

Maak een regel voor inkomend verkeer genaamd *myFrontendNSGRule* om inkomend webverkeer op *myFrontendVM* toe te staan met [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

```azurepowershell-interactive
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

U kunt het interne verkeer beperken tot *myBackendVM* vanaf uitsluitend *myFrontendVM* door een NSG te maken voor de back-endsubnet. In het volgende voorbeeld wordt een NSG-regel met de naam *myBackendNSGRule* gemaakt:

```azurepowershell-interactive
$nsgBackendRule = New-AzNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Voeg een netwerkbeveiligingsgroep toe genaamd *myFrontendNSG* met [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

```azurepowershell-interactive
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule
```

Voeg nu een netwerkbeveiligingsgroep toe genaamd *myBackendNSG* met New-AzNetworkSecurityGroup:

```azurepowershell-interactive
$nsgBackend = New-AzNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```

Voeg de netwerkbeveiligingsgroepen toe aan de subnetten:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
$frontendSubnet = $vnet.Subnets[0]
$backendSubnet = $vnet.Subnets[1]
$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myFrontendSubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend
$backendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name myBackendSubnet `
  -AddressPrefix $backendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgBackend
Set-AzVirtualNetwork -VirtualNetwork $vnet
```

## <a name="create-a-back-end-vm"></a>Een back-end virtuele machine maken

De eenvoudigste manier om de back-end-VM te maken voor deze zelfstudie is met een installatiekopie van SQL Server. In deze zelfstudie wordt alleen de VM gemaakt met de databaseserver, maar er wordt geen informatie gegeven over het krijgen van toegang tot de database.

Maak *myBackendNic*:

```azurepowershell-interactive
$backendNic = New-AzNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackend `
  -SubnetId $vnet.Subnets[1].Id
```

Stel met Get-Credential de gebruikersnaam en het wachtwoord in die nodig zijn voor het beheerdersaccount op de VM:

```azurepowershell-interactive
$cred = Get-Credential
```

Maak *myBackendVM*.

```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -Name myBackend `
   -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
   -ResourceGroupName myRGNetwork `
   -Location "EastUS" `
   -SubnetName MyBackendSubnet `
   -VirtualNetworkName myVNet
```

Op de installatiekopie die in dit voorbeeld wordt gebruikt is SQL Server geïnstalleerd, maar dit wordt niet in deze zelfstudie gebruikt. Het is opgenomen om u te laten zien hoe u een VM kunt configureren voor het verwerken van webverkeer en een VM voor het afhandelen van databasebeheer.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure-netwerken met betrekking tot virtuele machines gemaakt en beveiligd. 

> [!div class="checklist"]
> * Een virtueel netwerk en een subnet maken
> * Een openbaar IP-adres maken
> * Een front-end virtuele machine maken
> * Netwerkverkeer beveiligen
> * Een back-end virtuele machine maken

Zie [back-up en herstel na nood geval voor schijven](../backup-and-disaster-recovery-for-azure-iaas-disks.md)voor meer informatie over het beveiligen van uw VM-schijven.