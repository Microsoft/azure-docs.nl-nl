---
title: 'Een VNet verbinden met een ander VNet met behulp van een Azure VPN Gateway VNet-naar-VNet-verbinding: Power shell'
description: Virtuele netwerken met elkaar verbinden met behulp van een VNet-naar-VNet-verbinding en PowerShell.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 7de83302dd91d7d679b9c35718d184a9767ba436
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655354"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>Een VPN-gatewayverbinding tussen VNets configureren met behulp van PowerShell

Dit artikel helpt u om virtuele netwerken te verbinden met behulp van het verbindingstype VNet-naar-VNet. De virtuele netwerken kunnen zich in dezelfde of verschillende regio's bevinden en tot dezelfde of verschillende abonnementen behoren. Wanneer u VNets uit verschillende abonnementen koppelt, hoeven de abonnementen niet aan dezelfde Active Directory-tenant gekoppeld te zijn.

De stappen in dit artikel zijn van toepassing op het Resource Manager-implementatiemodel. Er wordt gebruikgemaakt van PowerShell. U kunt deze configuratie ook maken met een ander implementatiehulpprogramma of een ander implementatiemodel door in de volgende lijst een andere optie te selecteren:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure-CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassiek)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Verbinding maken tussen verschillende implementatiemodellen - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Verbinding maken tussen verschillende implementatiemodellen - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

## <a name="about-connecting-vnets"></a><a name="about"></a>Over het verbinden van VNet's

Er zijn meerdere manieren om VNet's te koppelen. In de onderstaande secties worden verschillende manieren beschreven voor het koppelen van virtuele netwerken.

### <a name="vnet-to-vnet"></a>VNet-naar-VNet

Het configureren van een VNet-naar-VNet-verbinding is een goede manier om VNet's te koppelen. Het verbinden van een virtueel netwerk met een ander virtueel netwerk met behulp van het VNet-naar-VNet-verbindingstype (VNet2VNet) is vergelijkbaar met het maken van een site-naar-site-IPsec-verbinding naar een on-premises locatie.  Voor beide verbindingstypen wordt een VPN-gateway gebruikt om een beveiligde tunnel met IPsec/IKE te bieden en beide typen werken tijdens het communiceren op dezelfde manier. Het verschil tussen de verbindingstypen is de manier waarop de lokale netwerkgateway is geconfigureerd. Als u een VNet-naar-VNet-verbinding maakt, ziet u de adresruimte voor het lokale netwerkgateway niet. Deze wordt automatisch gemaakt en ingevuld. Wanneer u de adresruimte voor een VNet bijwerkt, is de route naar de bijgewerkte adresruimte automatisch bekend voor het andere VNet. Als u een VNet-naar-VNet-verbinding maakt, is dit gewoonlijk sneller en makkelijker dan een site-naar-site-verbinding tussen VNet's.

### <a name="site-to-site-ipsec"></a>Site-naar-site (IPsec)

Als u met een gecompliceerde netwerkconfiguratie werkt, kunt u de VNet's wellicht beter koppelen met behulp van de stappen voor [site-naar-site](vpn-gateway-create-site-to-site-rm-powershell.md) dan met de stappen voor VNet-naar-VNet. Als u de stappen voor site-naar-site gebruikt, maakt en configureert u de lokale netwerkgateways handmatig. De lokale netwerkgateway voor elke VNet behandelt de andere VNet als een lokale site. Hiermee kunt u extra adresruimte voor de lokale netwerkgateway opgeven om het verkeer te routeren. Als de adresruimte voor een VNet wordt gewijzigd, moet u de bijbehorende lokale netwerkgateway bijwerken om de wijziging door te voeren. Er vindt niet automatisch een update plaats.

### <a name="vnet-peering"></a>VNet-peering

U kunt overwegen uw VNet's te koppelen door middel van VNET-peering. Bij VNET-peering wordt geen VPN-gateway gebruikt en er gelden diverse beperkingen voor. Bovendien worden de [prijzen voor VNET-peering](https://azure.microsoft.com/pricing/details/virtual-network) anders berekend dan voor [VNet-naar-VNet-VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Zie het artikel [VNet-peering](../virtual-network/virtual-network-peering-overview.md) voor meer informatie.

## <a name="why-create-a-vnet-to-vnet-connection"></a><a name="why"></a>Waarom een VNet-met-VNet-verbinding maken?

U kunt omwille van de volgende redenen virtuele netwerken koppelen met een VNet-naar-VNet-verbinding:

* **Geografische redundantie en aanwezigheid tussen regio's**

  * U kunt uw eigen geo-replicatie of synchronisatie met beveiligde connectiviteit instellen zonder gebruik te maken van internetgerichte eindpunten.
  * Met Azure Traffic Manager en Load Balancer kunt u workloads met maximale beschikbaarheid instellen met behulp van geografische redundantie over meerdere Azure-regio's. Een belangrijk voorbeeld hiervan is het instellen van SQL Always On met beschikbaarheidsgroepen verspreid over meerdere Azure-regio's.
* **Regionale toepassingen met meerdere lagen met isolatie- of beheergrenzen**

  * Binnen dezelfde regio kunt u vanwege isolatie- of beheervereisten toepassingen met meerdere lagen instellen met meerdere virtuele netwerken die met elkaar zijn verbonden.

VNet-naar-VNet-communicatie kan worden gecombineerd met configuraties voor meerdere locaties. Zo kunt u netwerktopologieën maken waarin cross-premises connectiviteit is gecombineerd met connectiviteit tussen virtuele netwerken.

## <a name="which-vnet-to-vnet-steps-should-i-use"></a><a name="steps"></a>Welke stappen voor VNet-naar-VNet moet ik gebruiken?

In dit artikel ziet u twee verschillende reeksen stappen. Eén reeks stappen voor [VNet's die onderdeel uitmaken van hetzelfde abonnement](#samesub) en één reeks voor [VNet's die onderdeel uitmaken van verschillende abonnementen](#difsub).
Het belangrijkste verschil tussen de reeksen is dat u afzonderlijke PowerShell-sessies moet gebruiken voor het configureren van verbindingen voor VNet's die onderdeel uitmaken van verschillende abonnementen. 

Voor deze oefening kunt u configuraties combineren of alleen de configuratie kiezen waarmee u wilt werken. Alle configuraties maken gebruik van het verbindingstype VNet-naar-VNet. Netwerkverkeer verloopt tussen de VNet's die rechtstreeks met elkaar zijn verbonden. In deze oefening wordt verkeer van TestVNet4 niet gerouteerd naar TestVNet5.

* [VNets die zich in hetzelfde abonnement bevinden: in](#samesub)de stappen voor deze configuratie wordt gebruikgemaakt van TestVNet1 en TestVNet4.

  ![Diagram met de V net-naar-V-net-stappen voor V-netten die zich in hetzelfde abonnement bevinden.](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [VNets die zich in verschillende abonnementen bevinden: in](#difsub)de stappen voor deze configuratie wordt gebruikgemaakt van TestVNet1 en TestVNet5.

  ![v2v-diagram](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="how-to-connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>VNets verbinden die tot hetzelfde abonnement behoren

### <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Omdat het Maxi maal 45 minuten duurt om een gateway te maken, Azure Cloud Shell time-out tijdens deze oefening. U kunt Cloud Shell opnieuw opstarten door in de linkerbovenhoek van de Terminal te klikken. Zorg ervoor dat u alle variabelen opnieuw declareert wanneer u de Terminal opnieuw opstart.

* Zie [Azure PowerShell installeren en configureren](/powershell/azure/)als u liever de nieuwste versie van de module Azure PowerShell lokaal installeert.

### <a name="step-1---plan-your-ip-address-ranges"></a><a name="Step1"></a>Stap 1: De IP-adresbereiken plannen

In de volgende stappen maakt u twee virtuele netwerken en de bijbehorende gatewaysubnetten en configuraties. Vervolgens maakt u een VPN-verbinding tussen de twee VNet's. Het is belangrijk dat u de IP-adresbereiken voor uw netwerkconfiguratie plant. De VNet-bereiken of de bereiken van het lokale netwerk mogen elkaar niet overlappen. In deze voorbeelden behandelen we geen DNS-server. Zie [Naamomzetting](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) als u naamomzetting voor uw virtuele netwerken wilt.

In de voorbeelden worden de volgende waarden gebruikt:

**Waarden voor TestVNet1:**

* VNet-naam: TestVNet1
* Resourcegroep: TestRG1
* Locatie: VS - oost
* TestVNet1: 10.11.0.0/16 en 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* GatewayName: VNet1GW
* Openbare IP: VNet1GWIP
* VPNType: op route gebaseerd
* Connection(1to4): VNet1toVNet4
* Connection(1to5): VNet1toVNet5 (voor VNets in verschillende abonnementen)
* ConnectionType: VNet2VNet

**Waarden voor TestVNet4:**

* VNet-naam: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Resourcegroep: TestRG4
* Locatie: VS - west
* GatewayName: VNet4GW
* Openbare IP: VNet4GWIP
* VPNType: op route gebaseerd
* Verbinding: VNet4toVNet1
* ConnectionType: VNet2VNet


### <a name="step-2---create-and-configure-testvnet1"></a><a name="Step2"></a>Stap 2: TestVNet1 maken en configureren

1. Controleer de instellingen van uw abonnement.

   Maak verbinding met uw account als u Power shell lokaal op uw computer uitvoert. Als u Azure Cloud Shell gebruikt, bent u automatisch verbonden.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Controleer de abonnementen voor het account.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Als u meer dan één abonnement hebt, geeft u het abonnement op dat u wilt gebruiken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName nameofsubscription
   ```
2. Declareer uw variabelen. In dit voorbeeld worden de variabelen gedeclareerd met de waarden voor deze oefening. In de meeste gevallen moet u de waarden vervangen door uw eigen waarden. U kunt deze variabelen echter gebruiken als u de stappen alleen wilt doorlopen om vertrouwd te raken met dit type configuratie. Wijzig de variabelen als dat nodig is en kopieer en plak ze vervolgens in uw PowerShell-console.

   ```azurepowershell-interactive
   $RG1 = "TestRG1"
   $Location1 = "East US"
   $VNetName1 = "TestVNet1"
   $FESubName1 = "FrontEnd"
   $BESubName1 = "Backend"
   $VNetPrefix11 = "10.11.0.0/16"
   $VNetPrefix12 = "10.12.0.0/16"
   $FESubPrefix1 = "10.11.0.0/24"
   $BESubPrefix1 = "10.12.0.0/24"
   $GWSubPrefix1 = "10.12.255.0/27"
   $GWName1 = "VNet1GW"
   $GWIPName1 = "VNet1GWIP"
   $GWIPconfName1 = "gwipconf1"
   $Connection14 = "VNet1toVNet4"
   $Connection15 = "VNet1toVNet5"
   ```
3. Maak een resourcegroep.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
4. Maak de subnetconfiguraties voor TestVNet1. In dit voorbeeld wordt een virtueel netwerk gemaakt met de naam TestVNet1. Er worden ook drie subnetten gemaakt, GatewaySubnet, FrontEnd en BackEnd. Wanneer u de waarden vervangt, is het belangrijk dat u de juiste namen voor de gatewaysubnets gebruikt, in het bijzonder GatewaySubnet. Als u een andere naam kiest, mislukt het maken van de gateway. Daarom wordt deze niet via de onderstaande variabele toegewezen.

   In het volgende voorbeeld worden de variabelen gebruikt die u eerder hebt ingesteld. In dit voorbeeld maakt het gatewaysubnet gebruik van een /27. Het is mogelijk om een klein gatewaysubnet van /29 te maken, maar we raden u aan een groter subnet met meer adressen te maken door ten minste /28 of /27 te selecteren. Hierdoor hebt u genoeg adressen voor mogelijke aanvullende toekomstige configuraties.

   ```azurepowershell-interactive
   $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
   $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
   $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix1
   ```
5. Maak TestVNet1.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
   ```
6. Vraag om een openbaar IP-adres toe te wijzen aan de gateway die u voor uw VNet gaat maken. De toewijzingsmethode is Dynamisch. U kunt het IP-adres dat u wilt gebruiken niet zelf opgeven. Het wordt dynamisch toegewezen aan uw gateway. 

   ```azurepowershell-interactive
   $gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AllocationMethod Dynamic
   ```
7. Maak de gatewayconfiguratie. De gatewayconfiguratie bepaalt welk subnet en openbaar IP-adres moeten worden gebruikt. Gebruik het voorbeeld om de gatewayconfiguratie te maken.

   ```azurepowershell-interactive
   $vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
   $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
   -Subnet $subnet1 -PublicIpAddress $gwpip1
   ```
8. Maak de gateway voor TestVNet1. In deze stap maakt u de virtuele netwerkgateway TestVNet1. VNet-naar-VNet-configuraties vereisen een op route gebaseerd VpnType. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
   -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

Nadat u de opdrachten hebt voltooid, duurt het Maxi maal 45 minuten om deze gateway te maken. Als u Azure Cloud Shell gebruikt, kunt u de Cloud Shell-sessie opnieuw starten door in de linkerbovenhoek van de Cloud Shell Terminal te klikken en vervolgens TestVNet4 te configureren. U hoeft niet te wachten totdat de TestVNet1-gateway is voltooid.

### <a name="step-3---create-and-configure-testvnet4"></a>Stap 3: TestVNet4 maken en configureren

Wanneer u TestVNet1 hebt geconfigureerd, maakt u TestVNet4. Volg de stappen hieronder en vervang de waarden door uw eigen waarden wanneer dat nodig is.

1. Verbind en Declareer uw variabelen. Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken.

   ```azurepowershell-interactive
   $RG4 = "TestRG4"
   $Location4 = "West US"
   $VnetName4 = "TestVNet4"
   $FESubName4 = "FrontEnd"
   $BESubName4 = "Backend"
   $VnetPrefix41 = "10.41.0.0/16"
   $VnetPrefix42 = "10.42.0.0/16"
   $FESubPrefix4 = "10.41.0.0/24"
   $BESubPrefix4 = "10.42.0.0/24"
   $GWSubPrefix4 = "10.42.255.0/27"
   $GWName4 = "VNet4GW"
   $GWIPName4 = "VNet4GWIP"
   $GWIPconfName4 = "gwipconf4"
   $Connection41 = "VNet4toVNet1"
   ```
2. Maak een resourcegroep.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG4 -Location $Location4
   ```
3. Maak de subnetconfiguraties voor TestVNet4.

   ```azurepowershell-interactive
   $fesub4 = New-AzVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
   $besub4 = New-AzVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
   $gwsub4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix4
   ```
4. Maak TestVNet4.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
   ```
5. Vraag een openbaar IP-adres aan.

   ```azurepowershell-interactive
   $gwpip4 = New-AzPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AllocationMethod Dynamic
   ```
6. Maak de gatewayconfiguratie.

   ```azurepowershell-interactive
   $vnet4 = Get-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
   $subnet4 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
   $gwipconf4 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
   ```
7. Maak de TestVNet4-gateway. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
   -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-4---create-the-connections"></a>Stap 4: de verbindingen maken

Wacht totdat beide gateways zijn voltooid. Start uw Azure Cloud Shell-sessie opnieuw op en kopieer en plak de variabelen vanaf het begin van stap 2 en stap 3 in de-console om waarden opnieuw te declareren.

1. Verkrijg beide gateways van het virtuele netwerk.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   $vnet4gw = Get-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
   ```
2. Maak de verbinding tussen TestVNet1 en TestVNet4. In deze stap maakt u de verbinding van TestVNet1 naar TestVNet4. Hier ziet u een gedeelde sleutel waarnaar wordt verwezen in de voorbeelden. U kunt uw eigen waarden voor de gedeelde sleutel gebruiken. Het belangrijkste is dat de gedeelde sleutel voor beide verbindingen moet overeenkomen. Het kan even duren voordat de verbinding is gemaakt.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
   -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
3. Maak de verbinding tussen TestVNet4 en TestVNet1. Deze stap is vergelijkbaar met die hierboven, alleen maakt u de verbinding nu vanuit TestVNet4 naar TestVNet1. Zorg dat de gedeelde sleutels overeenkomen. De verbinding wordt na enkele minuten tot stand gebracht.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
   -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. Controleer de verbinding. Raadpleeg de sectie [De verbinding controleren](#verify).

## <a name="how-to-connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>VNets verbinden die tot verschillende abonnement behoren

In dit scenario verbindt u TestVNet1 met TestVNet5. TestVNet1 en TestVNet5 bevinden zich in verschillende abonnementen. De abonnementen hoeven niet aan dezelfde Active Directory-tenant gekoppeld te zijn.

Het verschil tussen deze stappen en de eerste set is dat een deel van de configuratiestappen moet worden uitgevoerd in een aparte PowerShell-sessie in de context van het tweede abonnement. Dit geldt met name wanneer de twee abonnementen tot verschillende organisaties behoren.

Als gevolg van het wijzigen van de context van een abonnement in deze oefening, is het wellicht eenvoudiger om Power shell lokaal op uw computer te gebruiken, in plaats van de Azure Cloud Shell te gebruiken, wanneer u naar stap 8 gaat.

### <a name="step-5---create-and-configure-testvnet1"></a>Stap 5: TestVNet1 maken en configureren

U moet [Stap 1](#Step1) en [Stap 2](#Step2) van de vorige sessie uitvoeren om TestVNet1 en de VPN-gateway voor TestVNet1 te maken en te configureren. Voor deze configuratie is het niet vereist TestVNet4 uit de vorige sectie te maken. Als u deze wel maakt, levert dit geen problemen op met de volgende stappen. Nadat u stap 1 en stap 2 hebt voltooid, gaat u verder met stap 6 om TestVNet5 te maken.

### <a name="step-6---verify-the-ip-address-ranges"></a>Stap 6: de IP-adresbereiken controleren

Het is belangrijk dat u controleert of de IP-adresruimte van het nieuwe virtuele netwerk, TestVNet5, niet overlapt met een van de VNet-bereiken of de gatewaybereiken van het lokale netwerk. In dit voorbeeld kunnen de virtuele netwerken tot verschillende organisaties behoren. Voor deze oefening kunt u de volgende waarden voor TestVNet5 gebruiken:

**Waarden voor TestVNet5:**

* VNet-naam: TestVNet5
* Resourcegroep: TestRG5
* Locatie: Japan - oost
* TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* Openbare IP: VNet5GWIP
* VPNType: op route gebaseerd
* Verbinding: VNet5toVNet1
* ConnectionType: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>Stap 7: TestVNet5 maken en configureren

Deze stap moet worden uitgevoerd in de context van het nieuwe abonnement. Dit deel kan worden uitgevoerd door de beheerder in een andere organisatie die eigenaar is van het abonnement.

1. Declareer uw variabelen. Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken.

   ```azurepowershell-interactive
   $Sub5 = "Replace_With_the_New_Subscription_Name"
   $RG5 = "TestRG5"
   $Location5 = "Japan East"
   $VnetName5 = "TestVNet5"
   $FESubName5 = "FrontEnd"
   $BESubName5 = "Backend"
   $GWSubName5 = "GatewaySubnet"
   $VnetPrefix51 = "10.51.0.0/16"
   $VnetPrefix52 = "10.52.0.0/16"
   $FESubPrefix5 = "10.51.0.0/24"
   $BESubPrefix5 = "10.52.0.0/24"
   $GWSubPrefix5 = "10.52.255.0/27"
   $GWName5 = "VNet5GW"
   $GWIPName5 = "VNet5GWIP"
   $GWIPconfName5 = "gwipconf5"
   $Connection51 = "VNet5toVNet1"
   ```
2. Maak verbinding met abonnement 5. Open de PowerShell-console en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Controleer de abonnementen voor het account.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Geef het abonnement op dat u wilt gebruiken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName $Sub5
   ```
3. Een nieuwe resourcegroep maken.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG5 -Location $Location5
   ```
4. Maak de subnetconfiguraties voor TestVNet5.

   ```azurepowershell-interactive
   $fesub5 = New-AzVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
   $besub5 = New-AzVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
   $gwsub5 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
   ```
5. Maak TestVNet5.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
   -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
   ```
6. Vraag een openbaar IP-adres aan.

   ```azurepowershell-interactive
   $gwpip5 = New-AzPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
   -Location $Location5 -AllocationMethod Dynamic
   ```
7. Maak de gatewayconfiguratie.

   ```azurepowershell-interactive
   $vnet5 = Get-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
   $subnet5  = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
   $gwipconf5 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
   ```
8. Maak de TestVNet5-gateway.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
   -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-8---create-the-connections"></a>Stap 8: de verbindingen maken

Omdat de gateways in dit voorbeeld tot verschillende abonnementen behoren, is deze stap opgesplitst in twee PowerShell-sessies, aangeduid als [Abonnement 1] en [Abonnement 5].

1. **[Abonnement 1]** Verkrijg de gateway van het virtuele netwerk voor abonnement 1. Meld u aan en maak verbinding met abonnement 1 voordat u het volgende voor beeld uitvoert:

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   ```

   Kopieer de uitvoer van de volgende elementen en stuur deze via e-mail of een andere manier naar de beheerder van abonnement 5.

   ```azurepowershell-interactive
   $vnet1gw.Name
   $vnet1gw.Id
   ```

   De waarden van deze twee elementen lijken op die in het volgende voorbeeld:

   ```
   PS D:\> $vnet1gw.Name
   VNet1GW
   PS D:\> $vnet1gw.Id
   /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```
2. **[Abonnement 5]** Haal de gateway van het virtuele netwerk op voor abonnement 5. Meld u aan en maak verbinding met abonnement 5 voordat u het volgende voor beeld uitvoert:

   ```azurepowershell-interactive
   $vnet5gw = Get-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   ```

   Kopieer de uitvoer van de volgende elementen en stuur deze via e-mail of een andere manier naar de beheerder van Abonnement 1.

   ```azurepowershell-interactive
   $vnet5gw.Name
   $vnet5gw.Id
   ```

   De waarden van deze twee elementen lijken op die in het volgende voorbeeld:

   ```
   PS C:\> $vnet5gw.Name
   VNet5GW
   PS C:\> $vnet5gw.Id
   /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```
3. **[Abonnement 1]** Maak de TestVNet1 voor TestVNet5-verbinding. In deze stap maakt u de verbinding van TestVNet1 naar TestVNet5. Het verschil is hier dat $vnet5gw niet rechtstreeks kan worden verkregen omdat het zich in een ander abonnement bevindt. U moet een nieuw PowerShell-object maken met de waarden die in de bovenstaande stappen zijn gecommuniceerd vanuit Abonnement 1. Gebruik onderstaand voorbeeld. Vervang de naam, de ID en de gedeelde sleutel door uw eigen waarden. Het belangrijkste is dat de gedeelde sleutel voor beide verbindingen moet overeenkomen. Het kan even duren voordat de verbinding is gemaakt.

   Maak verbinding met abonnement 1 voordat u het volgende voorbeeld uitvoert:

   ```azurepowershell-interactive
   $vnet5gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet5gw.Name = "VNet5GW"
   $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
   $Connection15 = "VNet1toVNet5"
   New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. **[Abonnement 5]** Maak de TestVNet5 voor TestVNet1-verbinding. Deze stap is vergelijkbaar met die hierboven, alleen maakt u de verbinding nu vanuit TestVNet5 naar TestVNet1. Hier moet op dezelfde manier een PowerShell-object worden gemaakt op basis van de waarden die zijn verkregen van Abonnement 1. Zorg in deze stap dat de gedeelde sleutels overeenkomen.

   Maak verbinding met abonnement 5 voordat u het volgende voorbeeld uitvoert:

   ```azurepowershell-interactive
   $vnet1gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet1gw.Name = "VNet1GW"
   $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
   $Connection51 = "VNet5toVNet1"
   New-AzVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```

## <a name="how-to-verify-a-connection"></a><a name="verify"></a>Een verbinding controleren

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Veelgestelde vragen over VNet-naar-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Volgende stappen

* Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Raadpleeg de [documentatie over Virtual Machines](../index.yml) voor meer informatie.
* Voor meer informatie over BGP raadpleegt u [BGP Overview](vpn-gateway-bgp-overview.md) (BGP-overzicht) en [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (BGP configureren).