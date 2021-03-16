---
title: 'ExpressRoute en S2S VPN-verbindingen configureren: Azure PowerShell'
description: Configureer ExpressRoute en een site-naar-site-VPN-verbinding die naast Power shell kan worden gebruikt voor het Resource Manager-model.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/06/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 3b6ed39c11e3f90b986ef904ff3f8e9ff3158d0d
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574166"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>ExpressRoute-en site-naar-site-verbindingen configureren met Power shell
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [Power shell-klassiek](expressroute-howto-coexist-classic.md)
> 
> 

In dit artikel leest u hoe u ExpressRoute- en site-naar-site-VPN-verbindingen configureert die naast elkaar kunnen worden gebruikt. De mogelijkheid om site-naar-site-VPN en ExpressRoute te configureren heeft verschillende voordelen. U kunt een site-naar-site-VPN configureren als een beveiligd failoverpad voor ExpressRoute, of site-naar-site-VPN's gebruiken om verbinding te maken met sites die niet via ExpressRoute zijn verbonden. In dit artikel gaan we in op de stappen voor het configureren van beide scenario's. Dit artikel is van toepassing op het Resource Manager-implementatiemodel.

Configuratie van gelijktijdige site-naar-site-VPN- en ExpressRoute-verbindingen heeft verschillende voordelen:

* U kunt een site-naar-site-VPN configureren als een beveiligd failoverpad voor ExpressRoute. 
* U kunt ook site-naar-site-VPN's gebruiken om verbinding te maken met sites die niet zijn verbonden via ExpressRoute. 

In dit artikel worden de stappen beschreven voor het configureren van beide scenario's. Dit artikel is van toepassing op het Resource Manager-implementatiemodel en maakt gebruik van PowerShell. U kunt deze scenario's ook configureren met behulp van de Azure Portal, hoewel de documentatie nog niet beschikbaar is. U kunt eerst een van beide gateways configureren. Normaal gesp roken maakt u geen uitval tijd bij het toevoegen van een nieuwe gateway of gateway verbinding.

>[!NOTE]
>Als u een site-naar-site-VPN wilt maken via een ExpressRoute-circuit, raadpleegt u [dit artikel](site-to-site-vpn-over-microsoft-peering.md).
>

## <a name="limits-and-limitations"></a>Limieten en beperkingen
* **Alleen een op route gebaseerde VPN-gateway wordt ondersteund.** U moet een op route gebaseerde [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)gebruiken. U kunt ook een op route gebaseerde VPN-gateway gebruiken met een VPN-verbinding die is geconfigureerd voor ' op beleid gebaseerde verkeers selecters, zoals wordt beschreven in [verbinding maken met meerdere op beleid gebaseerde VPN-apparaten](../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).
* **De ASN van Azure VPN Gateway moet zijn ingesteld op 65515.** Azure VPN Gateway ondersteunt het BGP-routerings protocol. Voor een samen werking tussen ExpressRoute en Azure VPN moet u het autonome systeem nummer van uw Azure VPN-gateway op de standaard waarde 65515. Als u eerder een andere ASN dan 65515 hebt geselecteerd en u de instelling wijzigt in 65515, moet u de VPN-gateway opnieuw instellen om de instelling van kracht te laten worden.
* **Het gateway-subnet moet/27 of een kortere voor voegsel zijn**(zoals/26,/25) of u ontvangt een fout bericht wanneer u de gateway van het ExpressRoute-netwerk toevoegt.
* **Naast elkaar in een vnet met twee stacks wordt niet ondersteund.** Als u ExpressRoute IPv6-ondersteuning en een dual stack ExpressRoute-gateway gebruikt, is samen werken met VPN Gateway niet mogelijk.

## <a name="configuration-designs"></a>Configuratie-ontwerpen
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Een site-naar-site-VPN configureren als een failoverpad voor ExpressRoute
U kunt een site-naar-site-VPN-verbinding configureren als een back-up voor ExpressRoute. Deze verbinding geldt alleen voor virtuele netwerken die zijn gekoppeld aan het pad voor Azure Privépeering. Er is geen op VPN gebaseerde failoveroplossing voor services die toegankelijk zijn via Azure Microsoft-peering. Het ExpressRoute-circuit is altijd de primaire koppeling. Gegevens worden alleen via het site-naar-site-VPN-pad geleid als het ExpressRoute-circuit niet beschikbaar is. Om asymmetrische routering te voorkomen, moet ook in uw lokale netwerkconfiguratie de voorkeur zijn gegeven aan het ExpressRoute-circuit via de site-naar-site-VPN. U kunt het ExpressRoute-pad de voorkeur geven door een hogere lokale voorkeur in te stellen voor de routes die de ExpressRoute heeft ontvangen. 

>[!NOTE]
> Als u ExpressRoute micro soft-peering hebt ingeschakeld, kunt u het open bare IP-adres van uw Azure VPN-gateway ontvangen op de ExpressRoute-verbinding. Als u uw site-naar-site-VPN-verbinding als back-up wilt instellen, moet u uw on-premises netwerk configureren zodat de VPN-verbinding wordt doorgestuurd naar het internet.
>

> [!NOTE]
> Hoewel een ExpressRoute-circuit de voorkeur heeft boven site-naar-site-VPN wanneer beide routes hetzelfde zijn, gebruikt Azure de langste voorvoegselovereenkomst om de route naar de bestemming van het pakket te kiezen.
> 
> 

![Diagram waarin een site-naar-site-VPN-verbinding wordt weer gegeven als back-up voor ExpressRoute.](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Een site-naar-site-VPN configureren om verbinding te maken met sites die niet zijn verbonden via ExpressRoute
U kunt uw netwerk zodanig configureren dat sommige sites rechtstreeks verbinding maken met Azure via site-naar-site-VPN en sommige sites verbinding maken via ExpressRoute. 

![Naast elkaar gebruiken](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> U kunt een virtueel netwerk niet configureren als transitrouter.
> 
> 

## <a name="selecting-the-steps-to-use"></a>De stappen selecteren die u gaat gebruiken
Er zijn twee verschillende procedures waaruit u kunt kiezen. Welke configuratieprocedure u selecteert, is afhankelijk van het gegeven of u een nieuw virtueel netwerk wilt maken of een bestaand virtueel netwerk hebt waarmee u verbinding wilt maken.

* Ik heb geen VNet en moet er een maken.
  
    Als u nog geen virtueel netwerk hebt, begeleidt deze procedure u bij het maken van een nieuw virtueel netwerk met behulp van de Resource Manager-implementatie, en bij het maken van nieuwe ExpressRoute- en site-naar-site-VPN-verbindingen. Volg voor het configureren van een virtueel netwerk de stappen in [Een nieuw virtueel netwerk en naast elkaar bestaande verbindingen maken](#new).
* Ik heb al een VNet van het Resource Manager-implementatiemodel.
  
    Mogelijk hebt u al een virtueel netwerk met een bestaande site-naar-site-VPN-verbinding of ExpressRoute-verbinding. Als in dit scenario het subnetmasker van de gateway /28 of lager is (/28, /29, enz.), moet u de huidige gateway verwijderen. Het gedeelte [Naast elkaar bestaande verbindingen configureren voor een bestaand VNet](#add) begeleidt u bij het verwijderen van de gateway en vervolgens bij het maken van nieuwe ExpressRoute- en site-naar-site-VPN-verbindingen.
  
    Als u uw gateway verwijdert en opnieuw maakt, hebt u downtime voor uw cross-premises verbindingen. Terwijl u uw gateway configureert, kunnen uw virtuele machines en services echter nog steeds communiceren via de load balancer, als deze hiervoor zijn geconfigureerd.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]


## <a name="to-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>Een nieuw virtueel netwerk en naast elkaar bestaande verbindingen maken
Deze procedure begeleidt u bij het maken van een VNet en van site-naar-site- en ExpressRoute-verbindingen die naast elkaar kunnen worden gebruikt. De cmdlets die u voor deze configuratie gebruikt, kunnen enigszins afwijken van de cmdlets waarmee u bekend bent. Zorg ervoor dat u de cmdlets gebruikt die in deze instructies worden vermeld.

1. Meld u aan en selecteer uw abonnement.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Stel variabelen in.

   ```azurepowershell-interactive
   $location = "Central US"
   $resgrp = New-AzResourceGroup -Name "ErVpnCoex" -Location $location
   $VNetASN = 65515
   ```
3. Maak een virtueel netwerk met een gatewaysubnet. Zie [Een virtueel netwerk maken](../virtual-network/manage-virtual-network.md#create-a-virtual-network) voor meer informatie over het maken van een virtueel netwerk. Zie [Een subnet maken](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) voor meer informatie over het maken van subnets.
   
   > [!IMPORTANT]
   > Het gatewaysubnet moet /27 of een korter voorvoegsel (zoals /26 of /25) zijn.
   > 
   > 
   
    Maak een nieuw VNet.

   ```azurepowershell-interactive
   $vnet = New-AzVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
   ```
   
    Voeg subnetten toe.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Sla de VNet-configuratie op.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. <a name="vpngw"></a>Maak vervolgens uw site-naar-site-VPN-gateway. Zie [Een VNet met een site-naar-site-verbinding configureren](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) voor meer informatie over de configuratie van de VPN-gateway. De GatewaySku wordt alleen ondersteund voor de VPN-gateways *VpnGw1*, *VpnGw2*, *VpnGw3*, *Standard* en *HighPerformance*. ExpressRoute-VPN-Gateway-configuraties die naast elkaar bestaan, worden niet ondersteund in de basis-SKU. Het VpnType moet *RouteBased* zijn.

   ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
   ```
   
    Azure VPN-gateway biedt ondersteuning voor BGP-routeringsprotocol. U kunt voor dit virtuele netwerk een ASN (AS-nummer) opgeven door in de volgende opdracht de schakeloptie -Asn toe te voegen. Als u deze parameter niet opgeeft, is het AS-nummer standaard 65515.

   ```azurepowershell-interactive
   $azureVpn = New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
   ```
   
    U kunt het BGP peering IP- en het AS-nummer die Azure voor de VPN-gateway gebruikt, vinden in $azureVpn.BgpSettings.BgpPeeringAddress en $azureVpn.BgpSettings.Asn. Zie [BGP configureren](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) voor Azure VPN-gateway voor meer informatie.
5. Maak een lokaal exemplaar van de VPN-gateway van uw site. Deze opdracht wordt niet gebruikt om uw on-premises VPN-gateway te configureren. Met deze opdracht kunt u de instellingen voor de lokale gateway opgeven, zoals het openbare IP-adres en de on-premises-adresruimte, zodat de Azure VPN-gateway er verbinding mee kan maken.
   
    Als uw lokale VPN-apparaat alleen statische routering ondersteunt, kunt u de statische routes op de volgende manier configureren:

   ```azurepowershell-interactive
   $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   ```
   
    Als uw lokale VPN-apparaat de BGP ondersteunt en u dynamische ondersteuning wilt inschakelen, moet u het BGP peering-IP en het AS-nummer die uw lokale VPN-apparaat gebruikt, kennen.

   ```azurepowershell-interactive
   $localVPNPublicIP = "<Public IP>"
   $localBGPPeeringIP = "<Private IP for the BGP session>"
   $localBGPASN = "<ASN>"
   $localAddressPrefix = $localBGPPeeringIP + "/32"
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
   ```
6. Configureer het lokale VPN-apparaat om verbinding te maken met de nieuwe Azure VPN-gateway. Zie [VPN-apparaatconfiguratie](../vpn-gateway/vpn-gateway-about-vpn-devices.md) voor meer informatie over het configureren van een VPN-apparaat. 

7. Koppel de site-naar-site-VPN-gateway in Azure aan de lokale gateway.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   New-AzVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
   ```
 

8. Als u verbinding maakt met een bestaand ExpressRoute-circuit, slaat u stap 8 en 9 over en gaat u verder met stap 10. Configureer ExpressRoute-circuits. Zie [Een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md) voor meer informatie over het configureren van een ExpressRoute-circuit.


9. Configureer Azure Privépeering via het ExpressRoute-circuit. Zie [Peering configureren](expressroute-howto-routing-arm.md) voor meer informatie over het configureren van Azure Privépeering via het ExpressRoute-circuit

10. <a name="gw"></a>Maak een ExpressRoute-gateway. Zie [ExpressRoute-gatewayconfiguratie](expressroute-howto-add-gateway-resource-manager.md) voor meer informatie over de configuratie van de ExpressRoute-gateway. De GatewaySKU moet *Standard*, *HighPerformance* of *UltraPerformance* zijn.

    ```azurepowershell-interactive
    $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
    $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
    $gw = New-AzVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
    ```
11. Koppel de ExpressRoute-gateway aan het ExpressRoute-circuit. Nadat deze stap is voltooid, wordt de verbinding tussen uw on-premises netwerk en Azure tot stand gebracht via ExpressRoute. Zie [VNets koppelen aan ExpressRoute](expressroute-howto-linkvnet-arm.md) voor meer informatie over de koppelingsbewerking.

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
    New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
    ```

## <a name="to-configure-coexisting-connections-for-an-already-existing-vnet"></a><a name="add"></a>Naast elkaar bestaande verbindingen configureren voor een bestaand VNet
Als u een virtueel netwerk hebt met maar één virtuele netwerkgateway (bijvoorbeeld een site-naar-site VPN-gateway) en u een andere gateway van een ander type wilt toevoegen van (bijvoorbeeld een ExpressRoute-gateway), moet u de grootte van het gatewaysubnet controleren. Als het gatewaysubnet/27 of groter is, kunt u de onderstaande stappen overslaan en de stappen in de vorige sectie volgen om een site-naar-site VPN-gateway of een ExpressRoute-gateway toe te voegen. Als het gatewaysubnet /28 of /29 is, verwijdert u eerst de gateway van het virtuele netwerk en verhoogt u het gatewaysubnet. In de stappen in dit gedeelte wordt beschreven hoe u dat doet.

De cmdlets die u voor deze configuratie gebruikt, kunnen enigszins afwijken van de cmdlets waarmee u bekend bent. Zorg ervoor dat u de cmdlets gebruikt die in deze instructies worden vermeld.

1. Verwijder de bestaande ExpressRoute- of site-naar-site-VPN-gateway.

   ```azurepowershell-interactive 
   Remove-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
   ```
2. Verwijder het gatewaysubnet.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
   ```
3. Voeg een gatewaysubnet toe van /27 of hoger.
   
   > [!NOTE]
   > Als u in uw virtuele netwerk niet voldoende IP-adressen over hebt om het gatewaysubnet te vergroten, moet u meer IP-adresruimte toevoegen.
   > 
   > 

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Sla de VNet-configuratie op.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. U beschikt nu over een virtueel netwerk zonder gateways. Als u nieuwe gateways wilt maken en de verbindingen wilt instellen, gebruikt u de volgende voor beelden:

   Stel de variabelen in.

    ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   ```

   Maak de gateway.

   ```azurepowershell-interactive
   $gw = New-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup> -Location <yourlocation> -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
   ```

   Maak de verbinding.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
   New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
   ```

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Punt-naar-site-configuratie toevoegen aan de VPN-gateway

U kunt de onderstaande stappen volgen om punt-naar-site-configuratie toe te voegen aan uw VPN-gateway in een compatibiliteits installatie. Als u het VPN-basis certificaat wilt uploaden, moet u Power shell lokaal op uw computer installeren of de Azure Portal gebruiken.

1. Voeg een VPN-clientadresgroep toe.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
   ```
2. Upload het VPN- [basis certificaat](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#Certificates) naar Azure voor uw VPN-gateway. In dit voor beeld wordt ervan uitgegaan dat het basis certificaat is opgeslagen op de lokale computer waarop de volgende Power shell-cmdlets worden uitgevoerd en dat u Power shell lokaal uitvoert. U kunt het certificaat ook uploaden met behulp van de Azure Portal.

   ```powershell
   $p2sCertFullName = "RootErVpnCoexP2S.cer" 
   $p2sCertMatchName = "RootErVpnCoexP2S" 
   $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
   if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
   $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
   ```
Zie [Een punt-naar-site-verbinding configureren](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) voor meer informatie over punt-naar-site-VPN.

## <a name="to-enable-transit-routing-between-expressroute-and-azure-vpn"></a>Transit routering tussen ExpressRoute en Azure VPN inschakelen
Als u de connectiviteit wilt inschakelen tussen een van uw lokale netwerk dat is verbonden met ExpressRoute en een ander van uw lokale netwerk dat is verbonden met een site-naar-site-VPN-verbinding, moet u [Azure route server](../route-server/expressroute-vpn-support.md)instellen.


## <a name="next-steps"></a>Volgende stappen
Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over ExpressRoute.
