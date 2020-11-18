---
title: Azure Firewall implementeren in een hybride netwerk met behulp van Power shell & configureren
description: In dit artikel vindt u informatie over het implementeren en configureren van Azure Firewall met behulp van Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 08/28/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: e60c829831bde3b454ab180d1a39ec46cb346963
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658635"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Azure Firewall implementeren en configureren in een hybride netwerk met Azure PowerShell

Als u het on-premises netwerk verbindt met een virtueel Azure-netwerk om een hybride netwerk te maken, is de mogelijkheid om de toegang tot uw Azure-netwerkresources te beheren een belangrijk onderdeel van een algemeen beveiligingsplan.

U kunt Azure Firewall gebruiken om de netwerktoegang in een hybride netwerk te beheren met behulp van de regels die toegestaan en geweigerd netwerkverkeer definiëren.

Voor dit artikel maakt u drie virtuele netwerken:

- **VNet-Hub**: de firewall bevindt zich in dit virtuele netwerk.
- **VNet-spoke**: het virtuele spoke-netwerk vertegenwoordigt de workload die zich bevindt in Azure.
- **VNet-Onprem**: het on-premises virtuele netwerk vertegenwoordigt een on-premises netwerk. In een daadwerkelijke implementatie kan het zijn verbonden via een VPN of een ExpressRoute-verbinding. Ter vereenvoudiging maakt dit artikel gebruik van een VPN-gateway verbinding en wordt een virtueel netwerk met Azure-locatie gebruikt om een on-premises netwerk aan te duiden.

![Firewall in een hybride netwerk](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

In dit artikel leert u het volgende:

* De variabelen declareren
* Het virtuele hub-netwerk voor de firewall maken
* Het virtuele spoke-netwerk maken
* Het on-premises virtuele netwerk maken
* De firewall configureren en implementeren
* De VPN-gateways maken en verbinden
* De hub- en virtuele spoke-netwerken koppelen
* De routes maken
* De virtuele machines maken
* De firewall testen

Zie [zelf studie: Azure firewall implementeren en configureren in een hybride netwerk met behulp van de Azure Portal](tutorial-hybrid-portal.md)als u in plaats daarvan Azure Portal wilt gebruiken om deze zelf studie te volt ooien.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voor dit artikel moet u Power shell lokaal uitvoeren. U moet de module Azure PowerShell hebben geïnstalleerd. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Nadat u de versie van PowerShell hebt gecontroleerd, voert u `Login-AzAccount` uit om een verbinding op te zetten met Azure.

Er zijn drie belangrijke vereisten voor de correcte werking van dit scenario:

- Een door de gebruiker gedefinieerde route (UDR) in het spoke-subnet die verwijst naar het IP-adres van Azure Firewall als de standaardgateway. De route doorgifte van de virtuele netwerk gateway moet worden **uitgeschakeld** voor deze route tabel.
- Een door de gebruiker gedefinieerde route (UDR) in het subnet van de hubgateway moet verwijzen naar het IP-adres van de firewall als de volgende hop naar de spoke-netwerken.

   Er is geen door de gebruiker gedefinieerde route (UDR) nodig in het Azure Firewall-subnet, omdat het de routes overneemt van BGP.
- Zorg dat u **AllowGatewayTransit** instelt voor de peering van VNet-Hub naar VNet-Spoke en **UseRemoteGateways** voor de peering van VNet-Spoke naar VNet-Hub.

Zie de sectie [routes maken](#create-the-routes) in dit artikel om te zien hoe deze routes worden gemaakt.

>[!NOTE]
>Azure Firewall moet een directe verbinding met internet hebben. Als uw AzureFirewallSubnet een standaardroute naar uw on-premises netwerk via BGP leert, moet u deze overschrijven met een UDR van 0.0.0.0/0 met de waarde **NextHopType** ingesteld op **Internet** om directe verbinding met internet te houden.
>
>Azure Firewall kan worden geconfigureerd voor ondersteuning van geforceerde tunneling. Zie [Geforceerde tunneling van Azure Firewall](forced-tunneling.md) voor meer informatie.

>[!NOTE]
>Verkeer tussen rechtstreeks gepeerde VNets wordt rechtstreeks gerouteerd, zelfs als de UDR naar Azure Firewall als standaardgateway wijst. Als u in dit scenario subnet-naar-subnet-verkeer wilt verzenden, moet een UDR het voorvoegsel van het doelsubnetwerk expliciet op beide subnetten bevatten.

Zie [Naslagdocumentatie voor Azure PowerShell](/powershell/module/az.network/new-azfirewall) om de gerelateerde naslaginformatie voor Azure PowerShell te bekijken.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="declare-the-variables"></a>De variabelen declareren

In het volgende voor beeld worden de variabelen gedeclareerd met de waarden voor dit artikel. In sommige gevallen moet u mogelijk bepaalde waarden vervangen door uw eigen waarden om in uw abonnement te kunnen werken. Wijzig de variabelen als dat nodig is en kopieer en plak ze vervolgens in uw PowerShell-console.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>Het virtuele hub-netwerk voor de firewall maken

Maak eerst de resource groep die de resources voor dit artikel bevat:

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

Definieer de subnetten die moeten worden opgenomen in het virtuele netwerk:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Maak nu het virtuele hub-netwerk voor de firewall:

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Vraag een openbaar IP-adres aan dat moet worden toegewezen aan de VPN-gateway die u voor uw virtuele netwerk maakt. De *AllocationMethod* is **Dynamisch**. U kunt het IP-adres dat u wilt gebruiken niet zelf opgeven. Het wordt dynamisch toegewezen aan uw VPN-gateway.

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Het virtuele spoke-netwerk maken

Definieer de subnetten die moeten worden opgenomen in de virtuele spoke-netwerken:

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Het virtuele spoke-netwerk maken:

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Het on-premises virtuele netwerk maken

Definieer de subnetten die moeten worden opgenomen in het virtuele netwerk:

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Maak nu het on-premises virtuele netwerk:

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Vraag een openbaar IP-adres aan dat moet worden toegewezen aan de gateway die u voor het virtuele netwerk gaat maken. De *AllocationMethod* is **Dynamisch**. U kunt het IP-adres dat u wilt gebruiken niet zelf opgeven. Het wordt dynamisch toegewezen aan uw gateway.

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>De firewall configureren en implementeren

Implementeer nu de het virtuele hub-netwerk voor de firewall.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Netwerkregels configureren

<!--- $Rule3 = New-AzFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>De VPN-gateways maken en verbinden

Het virtuele hub-netwerk en het on-premises virtuele netwerk zijn verbonden via VPN-gateways.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Een VPN-gateway maken voor het virtuele hub-netwerk

Maak de gatewayconfiguratie. De VPN-gatewayconfiguratie bepaalt welk subnet en openbaar IP-adres moeten worden gebruikt.

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Maak nu een VPN-gateway voor het virtuele hub-netwerk. Voor netwerk-naar-netwerk-configuraties is een RouteBased VpnType vereist. Het maken van een VPN-gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde VPN-gateway-SKU.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Een VPN-gateway maken voor het on-premises virtuele netwerk

Maak de gatewayconfiguratie. De VPN-gatewayconfiguratie bepaalt welk subnet en openbaar IP-adres moeten worden gebruikt.

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Maak nu de VPN-gateway voor het on-premises virtuele netwerk. Voor netwerk-naar-netwerk-configuraties is een RouteBased VpnType vereist. Het maken van een VPN-gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde VPN-gateway-SKU.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>De VPN-verbindingen maken

U kunt nu de VPN-verbindingen maken tussen de hub-gateway en de on-premises gateway

#### <a name="get-the-vpn-gateways"></a>VPN-gateways ophalen

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>De verbindingen maken

In deze stap maakt u de verbinding van het virtuele hub-netwerk naar het on-premises virtuele netwerk. Hier ziet u een gedeelde sleutel waarnaar wordt verwezen in de voorbeelden. U kunt uw eigen waarden voor de gedeelde sleutel gebruiken. Het belangrijkste is dat de gedeelde sleutel voor beide verbindingen moet overeenkomen. Het kan even duren voordat de verbinding is gemaakt.

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Maak de verbinding van het on-premises virtuele netwerk naar het virtuele hub-netwerk. Deze stap is vergelijkbaar met de vorige, behalve dat u de verbinding maakt vanuit VNet-Onprem naar VNet-hub. Zorg dat de gedeelde sleutels overeenkomen. De verbinding wordt na enkele minuten tot stand gebracht.

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>De verbinding controleren

U kunt een geslaagde verbinding controleren met behulp van de cmdlet *Get-AzVirtualNetworkGatewayConnection* , met of zonder *-debug*. Gebruik het volgende cmdlet-voorbeeld om de waarden aan te passen aan uw eigen waarden. Selecteer **A** als dit wordt gevraagd om **alles** uit te voeren. In het voor beeld verwijst *-name* naar de naam van de verbinding die u wilt testen.

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Bekijk de waarden nadat de cmdlet is voltooid. In het onderstaande voorbeeld wordt de verbindingsstatus weergegeven als *Verbonden* en ziet u inkomende en uitgaande bytes.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>De hub- en virtuele spoke-netwerken koppelen

Koppel nu de virtuele hub- en spoke-netwerken.

```azurepowershell
# Peer hub to spoke
Add-AzVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>De routes maken

Maak nu een paar routes:

- Een route van het subnet van de hub-gateway naar het spoke-subnet via het IP-adres van de firewall
- Een standaardroute van het spoke-subnet via het IP-adres van de firewall

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled. The property is now called "Virtual network gateway route propagation," but the API still refers to the parameter as "DisableBgpRoutePropagation."
$routeTableSpokeDG = New-AzRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzRouteConfig `
  -Name "ToFirewall" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak nu de spoke-workloadmachines en on-premises virtuele machines en plaats ze in de toepasselijke subnetten.

### <a name="create-the-workload-virtual-machine"></a>De virtuele workloadmachine maken

Maak in het virtuele spoke-netwerk een virtuele machine waarop IIS wordt uitgevoerd, zonder openbaar IP-adres en waarop inkomende pings zijn toegestaan.
Wanneer u daarom wordt gevraagd, typt u een gebruikersnaam en wachtwoord voor de virtuele machine.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>De on-premises virtuele machine maken

Dit is een eenvoudige virtuele machine waarmee u verbinding kunt maken met het openbare IP-adres via Extern bureaublad. Vanaf daar maakt u vervolgens verbinding met de on-premises server via de firewall. Wanneer u daarom wordt gevraagd, typt u een gebruikersnaam en wachtwoord voor de virtuele machine.

```azurepowershell
New-AzVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>De firewall testen

Haal eerst het privé-IP-adres van de virtuele machine **VM-spoke-01** op en noteer dit.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Maak vanuit de Azure-portal verbinding met de virtuele machine **VM-OnPrem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Open een webbrowser op **VM-OnPrem** en ga naar http://\<VM-spoke-01 private IP\>.

De standaardpagina van Internet Information Services moet worden weergegeven.

Maak vanaf **VM-Onprem** met Extern bureaublad verbinding met **VM-spoke-01** op het privé-IP-adres.

Deze verbinding moet worden gemaakt en u moet zich kunnen aanmelden met uw gekozen gebruikersnaam en wachtwoord.

Nu u hebt geverifieerd dat de firewallregels werken:

<!---- You can ping the server on the spoke VNet.--->
- U kunt de bladeren op de webserver in het virtuele spoke-netwerk.
- U kunt verbinding maken met de server in het virtuele spoke-netwerk met behulp van RDP.

Wijzig vervolgens de verzameling netwerkregels van de firewall in **Weigeren** om te controleren of de regels werken zoals verwacht. Voer het volgende script uit om de actie van het verzamelen van regels te wijzigen in **Weigeren**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

Voer nu de tests opnieuw uit. Ze moeten deze keer allemaal mislukken. Sluit eventuele externe bureaubladen voordat u de gewijzigde regels test.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de firewall-resources voor de volgende zelfstudie bewaren. Als u ze niet meer nodig hebt, verwijdert u de resourcegroep **FW-Hybrid-Test** om alle firewall-gerelateerde resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Als volgende kunt u de Azure Firewall-logboeken bewaken.

[Zelfstudie: Azure Firewall-logboeken bewaken](./firewall-diagnostics.md)