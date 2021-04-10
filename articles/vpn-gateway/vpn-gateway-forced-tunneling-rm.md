---
title: Geforceerde tunneling configureren voor site-naar-site-verbindingen
description: Alle Internet verkeer omleiden (forceren) naar uw on-premises locatie.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cherylmc
ms.openlocfilehash: afd1c1d5312a9fbf39b401b0cbb4b9997f27407a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869035"
---
# <a name="configure-forced-tunneling"></a>Geforceerde tunneling configureren

Met geforceerde tunneling kunt u alle voor internet bestemde verkeer geforceerd terugsturen naar uw on-premises locatie via een site-naar-site-VPN-tunnel voor inspectie en controle. Dit is een kritieke beveiligings vereiste voor de meeste IT-beleids regels van ondernemingen. Als u geforceerde tunneling niet configureert, gaat Internet-gebonden verkeer van uw virtuele machines in azure altijd rechtstreeks door naar Internet en zonder de optie om het verkeer te controleren of te controleren. Onbevoegde toegang tot internet kan mogelijk leiden tot vrijgeven van informatie of andere typen beveiligings Risico's.

Geforceerde tunneling kan worden geconfigureerd met behulp van Azure PowerShell. Het kan niet worden geconfigureerd met behulp van de Azure Portal. Dit artikel helpt u bij het configureren van geforceerde tunneling voor virtuele netwerken die zijn gemaakt met het Resource Manager-implementatie model. Zie [geforceerde tunneling-Classic](vpn-gateway-about-forced-tunneling.md)als u geforceerde tunneling wilt configureren voor het klassieke implementatie model.

## <a name="about-forced-tunneling"></a>Over geforceerde tunneling

In het volgende diagram ziet u hoe geforceerde tunneling werkt.

:::image type="content" source="./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png" alt-text="Diagram toont geforceerde tunneling.":::

In dit voor beeld is het frontend-subnet niet geforceerde tunneling. De werk belastingen in het frontend-subnet kunnen rechtstreeks blijven accepteren en reageren op aanvragen van de klant via internet. De subnetten van de middelste laag en de back-end worden geforceerd getunneld. Uitgaande verbindingen van deze twee subnetten met Internet worden via een van de site-naar-site (S2S) VPN-tunnels geforceerd of omgeleid naar een on-premises site.

Hierdoor kunt u Internet toegang beperken en inspecteren vanuit uw virtuele machines of Cloud Services in azure, terwijl u doorgaat met het inschakelen van de service architectuur met meerdere lagen. Als uw virtuele netwerken geen Internet gerichte workloads hebben, kunt u ook geforceerde tunneling Toep assen op de gehele virtuele netwerken.

## <a name="requirements-and-considerations"></a>Vereisten en overwegingen

Geforceerde Tunneling in azure wordt geconfigureerd met de aangepaste door de gebruiker gedefinieerde routes van het virtuele netwerk. Het omleiden van verkeer naar een on-premises site wordt uitgedrukt als een standaard route naar de Azure VPN-gateway. Zie aangepaste door de [gebruiker gedefinieerde routes](../virtual-network/virtual-networks-udr-overview.md#user-defined)voor meer informatie over door de gebruiker gedefinieerde route ring en virtuele netwerken.

* Elk subnet van het virtuele netwerk heeft een ingebouwde systeem routerings tabel. De systeem routerings tabel heeft de volgende drie groepen routes:
  
  * **Lokale VNet-routes:** Rechtstreeks naar de doel-Vm's in hetzelfde virtuele netwerk.
  * **On-premises routes:** Naar de Azure VPN-gateway.
  * **Standaard route:** Rechtstreeks op internet. Pakketten die bestemd zijn voor de privé-IP-adressen die niet worden gedekt door de vorige twee routes, worden verwijderd.
* Deze procedure maakt gebruik van door de gebruiker gedefinieerde routes (UDR) om een routerings tabel te maken om een standaard route toe te voegen en koppel de routerings tabel vervolgens aan uw VNet-subnet ('s) om geforceerde Tunneling in te scha kelen op deze subnetten.
* Geforceerde tunneling moet worden gekoppeld aan een VNet met een op route gebaseerde VPN-gateway. U moet een "standaard site" instellen tussen de cross-premises lokale sites die zijn verbonden met het virtuele netwerk. Het on-premises VPN-apparaat moet ook worden geconfigureerd met 0.0.0.0/0 als verkeers selectie. 
* ExpressRoute geforceerde tunneling is niet geconfigureerd via dit mechanisme, maar wordt in plaats daarvan ingeschakeld door een standaard route te adverteren via de BGP-peering-sessies van ExpressRoute. Zie de [ExpressRoute-documentatie](https://azure.microsoft.com/documentation/services/expressroute/)voor meer informatie.
* Wanneer u zowel de VPN Gateway-als de ExpressRoute-gateway in hetzelfde VNet hebt geïmplementeerd, zijn door de gebruiker gedefinieerde routes (UDR) niet meer nodig omdat de ExpressRoute-gateway de configuratie ' standaard site ' in VNet adverteert.

## <a name="configuration-overview"></a>Configuratieoverzicht

De volgende procedure helpt u bij het maken van een resource groep en een VNet. U gaat vervolgens een VPN-gateway maken en geforceerde tunneling configureren. In deze procedure heeft het virtuele netwerk ' Multi laag-VNet ' drie subnetten: ' front-end ', ' het midtier ' en ' back ', met vier cross-premises verbindingen: ' DefaultSiteHQ ' en drie vertakkingen.

De procedure stappen stellen de ' DefaultSiteHQ ' in als de standaard site verbinding voor geforceerde tunneling en configureren van de subnetten ' het midtier ' en ' back-end ' om geforceerde tunneling te gebruiken.

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

Installeer de meest recente versie van de PowerShell-cmdlets van Azure Resource Manager. Zie [How to install and configure Azure PowerShell](/powershell/azure/) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van de PowerShell-cmdlets.

> [!IMPORTANT]
> De meest recente versie van de Power shell-cmdlets moet worden geïnstalleerd. Anders worden er validatie fouten weer gegeven wanneer u een aantal van de cmdlets uitvoert.
>
>

### <a name="to-sign-in"></a>Aanmelden

[!INCLUDE [Sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Geforceerde tunneling configureren

> [!NOTE]
> Er worden mogelijk waarschuwingen weer gegeven met de melding ' het object type van de uitvoer van deze cmdlet wordt gewijzigd in een toekomstige versie '. Dit is normaal gedrag en u kunt deze waarschuwingen veilig negeren.
>
>


1. Een resourcegroep maken.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Maak een virtueel netwerk en geef subnetten op.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Maak de lokale netwerk gateways.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Maak de route tabel en de route regel.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Koppel de route tabel aan de subnetten het midtier en back-end.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Maak de gateway van het virtuele netwerk. Het kan enige tijd duren voordat deze stap is voltooid, soms 45 minuten of meer, omdat u de gateway maakt en configureert. Als u Validatieset-fouten ziet met betrekking tot de waarde GatewaySKU, controleert u of u de [meest recente versie van de Power shell-cmdlets](#before)hebt geïnstalleerd. De meest recente versie van de Power shell-cmdlets bevat de nieuwe gevalideerde waarden voor de nieuwste gateway-Sku's.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Wijs een standaard site toe aan de gateway van het virtuele netwerk. De **-GatewayDefaultSite** is de cmdlet-para meter waarmee de geforceerde routerings configuratie kan worden uitgevoerd. Zorg er daarom voor dat u deze instelling correct configureert. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Breng de site-naar-site-VPN-verbindingen tot stand.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
