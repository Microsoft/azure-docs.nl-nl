---
title: 'Azure VPN Gateway: Gateways verbinden met meerdere on-premises op beleid gebaseerde VPN-apparaten'
description: Configureer een op Azure route gebaseerde VPN-gateway naar meerdere op beleid gebaseerde VPN-apparaten met behulp van Azure Resource Manager en Power shell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 2a85204fef026940394a19934bef1c631a8e2d21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89418872"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Azure VPN-gateways verbinden met meerdere on-premises op beleid gebaseerde VPN-apparaten met behulp van Power shell

Dit artikel helpt u bij het configureren van een op Azure route gebaseerde VPN-gateway om verbinding te maken met meerdere on-premises op beleid gebaseerde VPN-apparaten met aangepaste IPsec/IKE-beleids regels op S2S VPN-verbindingen.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>Over op beleid gebaseerde en op route gebaseerde VPN-gateways

Op beleid gebaseerde en *op route gebaseerde* VPN-apparaten verschillen in hoe de selecters van het IPSec-verkeer worden ingesteld op een verbinding:

* **Op beleid gebaseerd** VPN-apparaten gebruiken de combi Naties van voor voegsels van beide netwerken om te definiëren hoe verkeer wordt versleuteld/ontsleuteld via IPsec-tunnels. Het is doorgaans gebaseerd op Firewall apparaten die pakket filters uitvoeren. IPsec-tunnelversleuteling en -ontsleuteling worden toegevoegd aan de engine voor pakketfiltering en verwerking.
* **Op basis van route** VPN-apparaten gebruiken any-to-any-verkeer (joker tekens) en kunnen route ring/doorstuur tabellen direct verkeer naar verschillende IPsec-tunnels. Het is doorgaans gebaseerd op router platforms waar elke IPsec-tunnel wordt gemodelleerd als een netwerk interface of VTI (virtuele tunnel Interface).

De volgende diagrammen markeren de twee modellen:

### <a name="policy-based-vpn-example"></a>Voor beeld van op beleid gebaseerd VPN
![op beleid gebaseerd](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Voor beeld van op route gebaseerd VPN
![op basis van route](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Azure-ondersteuning voor op beleid gebaseerde VPN-verbindingen
Azure ondersteunt momenteel beide modi van VPN-gateways: op route gebaseerde VPN-gateways en op beleid gebaseerde VPN-gateways. Ze zijn gebouwd op verschillende interne platforms, wat resulteert in verschillende specificaties:

| Categorie | PolicyBased VPN Gateway | RouteBased VPN Gateway | RouteBased VPN Gateway |
| -------- | ----------------------- | ---------------------- | ---------------------- |---                                                 |
| **Azure gateway-SKU**    | Basic                       | Basic                            | VpnGw1, VpnGw2, VpnGw3, VpnGw4, VpnGw5  |
| **IKE-versie**          | IKEv1                       | IKEv2                            | IKEv1 en IKEv2                         |
| **Max. S2S-verbindingen** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

Met het aangepaste IPsec/IKE-beleid kunt u nu VPN-gateways op basis van Azure op basis van route ring configureren voor het gebruik van op voor voegsels gebaseerde verkeers selecties met de optie '**PolicyBasedTrafficSelectors**', om verbinding te maken met on-premises op beleid gebaseerde VPN-apparaten. Met deze mogelijkheid kunt u verbinding maken vanaf een virtueel Azure-netwerk en een VPN-gateway naar meerdere on-premises op beleid gebaseerde VPN/Firewall-apparaten, waarbij u de limiet voor enkele verbindingen verwijdert van de huidige VPN-gateways op basis van Azure-beleid.

> [!IMPORTANT]
> 1. Om deze connectiviteit in te scha kelen, moeten uw on-premises op beleid gebaseerde VPN-apparaten **IKEv2** ondersteunen om verbinding te maken met de op Azure route gebaseerde VPN-gateways. Controleer de specificaties van uw VPN-apparaat.
> 2. De on-premises netwerken die verbinding maken via op beleid gebaseerde VPN-apparaten met dit mechanisme kunnen alleen verbinding maken met het virtuele netwerk van Azure. **ze kunnen niet door voer naar andere on-premises netwerken of virtuele netwerken via dezelfde Azure VPN-gateway**.
> 3. De configuratie optie maakt deel uit van het aangepaste IPsec/IKE-verbindings beleid. Als u de optie op beleid gebaseerde verkeers selectie inschakelt, moet u het volledige beleid (IPsec/IKE-versleuteling en integriteits algoritmen, sleutel sterkte en SA-levens duur) opgeven.

In het volgende diagram ziet u waarom Transit routering via Azure VPN-gateway niet werkt met de op beleid gebaseerde optie:

![op beleid gebaseerde door Voer](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Zoals in het diagram wordt weer gegeven, bevat de Azure VPN-gateway verkeers selecties van het virtuele netwerk naar elk van de on-premises netwerk voorvoegsels, maar niet de voor voegsels voor cross-Connection. Bijvoorbeeld: on-premises site 2, site 3 en site 4 kunnen elk VNet1 respectievelijk communiceren, maar kunnen geen verbinding maken via de Azure VPN-gateway. In het diagram ziet u de selectie vakjes voor kruis verbindingen verkeer die niet beschikbaar zijn in de Azure VPN-gateway onder deze configuratie.

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

De instructies in dit artikel volgen hetzelfde voor beeld zoals beschreven in [IPSec/IKE-beleid configureren voor S2S-of vnet-naar-vnet-verbindingen](vpn-gateway-ipsecikepolicy-rm-powershell.md) om een S2S-VPN-verbinding tot stand te brengen. Dit wordt weer gegeven in het volgende diagram:

![S2S-beleid](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

De werk stroom voor het inschakelen van deze verbinding:
1. Maak het virtuele netwerk, de VPN-gateway en de lokale netwerk gateway voor uw cross-premises verbinding.
2. Een IPsec/IKE-beleid maken.
3. Pas het beleid toe wanneer u een S2S-of VNet-naar-VNet-verbinding maakt en **Schakel de op beleid gebaseerde verkeers selectie vakjes** in voor de verbinding.
4. Als de verbinding al is gemaakt, kunt u het beleid Toep assen of bijwerken voor een bestaande verbinding.

## <a name="before-you-begin"></a>Voordat u begint

* Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>Op beleid gebaseerde verkeers selectie inschakelen

In deze sectie wordt beschreven hoe u op beleid gebaseerde verkeers selectie vakjes inschakelt voor een verbinding. Zorg ervoor dat u [deel 3 van het artikel IPSec/IKE-beleid configureren](vpn-gateway-ipsecikepolicy-rm-powershell.md)hebt voltooid. Voor de stappen in dit artikel worden dezelfde para meters gebruikt.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Stap 1: het virtuele netwerk, de VPN-gateway en de lokale netwerk gateway maken

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Verbinding maken met uw abonnement en uw variabelen declareren

1. Als u Power shell lokaal op uw computer uitvoert, meldt u zich aan met de cmdlet *Connect-AzAccount* . Of gebruik in plaats daarvan Azure Cloud Shell in uw browser.

2. Declareer uw variabelen. Voor deze oefening gebruiken we de volgende variabelen:

   ```azurepowershell-interactive
   $Sub1          = "<YourSubscriptionName>"
   $RG1           = "TestPolicyRG1"
   $Location1     = "East US 2"
   $VNetName1     = "TestVNet1"
   $FESubName1    = "FrontEnd"
   $BESubName1    = "Backend"
   $GWSubName1    = "GatewaySubnet"
   $VNetPrefix11  = "10.11.0.0/16"
   $VNetPrefix12  = "10.12.0.0/16"
   $FESubPrefix1  = "10.11.0.0/24"
   $BESubPrefix1  = "10.12.0.0/24"
   $GWSubPrefix1  = "10.12.255.0/27"
   $DNS1          = "8.8.8.8"
   $GWName1       = "VNet1GW"
   $GW1IPName1    = "VNet1GWIP1"
   $GW1IPconf1    = "gw1ipconf1"
   $Connection16  = "VNet1toSite6"
   $LNGName6      = "Site6"
   $LNGPrefix61   = "10.61.0.0/16"
   $LNGPrefix62   = "10.62.0.0/16"
   $LNGIP6        = "131.107.72.22"
   ```

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Het virtuele netwerk, de VPN-gateway en de lokale netwerk gateway maken

1. Een resourcegroep maken.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. Gebruik het volgende voor beeld om de TestVNet1 van het virtuele netwerk met drie subnetten en de VPN-gateway te maken. Als u waarden wilt vervangen, is het belang rijk dat u het subnet van de gateway altijd een naam GatewaySubnet. Als u een andere naam kiest, mislukt het maken van de gateway.

    ```azurepowershell-interactive
    $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
    
    New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
    
    $gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    
    New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
    
    New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
    ```

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>Stap 2: een S2S-VPN-verbinding maken met een IPsec/IKE-beleid

1. Een IPsec/IKE-beleid maken.

   > [!IMPORTANT]
   > U moet een IPsec/IKE-beleid maken om de optie ' UsePolicyBasedTrafficSelectors ' in te scha kelen voor de verbinding.

   In het volgende voor beeld wordt een IPsec/IKE-beleid met deze algoritmen en para meters gemaakt:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS geen, SA-levens duur van 14400 seconden & 102400000KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Maak de S2S VPN-verbinding met op beleid gebaseerde verkeers selecties en IPsec/IKE-beleid en pas het IPsec/IKE-beleid toe dat in de vorige stap is gemaakt. Houd rekening met de extra para meter-UsePolicyBasedTrafficSelectors $True, waarmee op beleid gebaseerde verkeers selecties worden ingeschakeld op de verbinding.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. Na het volt ooien van de stappen maakt de S2S VPN-verbinding gebruik van de gedefinieerde IPsec/IKE-beleid en schakelt u op beleid gebaseerde verkeers selectie vakjes in voor de verbinding. U kunt dezelfde stappen herhalen om meer verbindingen toe te voegen aan extra on-premises op beleid gebaseerde VPN-apparaten vanuit dezelfde Azure VPN-gateway.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>Voor het bijwerken van op beleid gebaseerde verkeers selecters
In deze sectie wordt beschreven hoe u de optie voor het op beleid gebaseerde verkeers selectie bijwerkt voor een bestaande S2S-VPN-verbinding.

1. Haal de verbindings bron op.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Bekijk de optie op beleid gebaseerde verkeers selectie.
De volgende regel geeft aan of de op beleid gebaseerde verkeers selecties worden gebruikt voor de verbinding:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Als de regel '**True**' retourneert, worden op beleid gebaseerde verkeers selecties geconfigureerd op de verbinding. anders wordt '**False**' geretourneerd.
1. Zodra u de verbindings bron hebt opgehaald, kunt u de op beleid gebaseerde verkeers selectie vakjes op een verbinding in-of uitschakelen.

   - Om in te scha kelen

      In het volgende voor beeld wordt de optie op beleid gebaseerde verkeers selectie ingeschakeld, maar blijft het IPsec/IKE-beleid ongewijzigd:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - Uitschakelen

      In het volgende voor beeld wordt de optie voor het op beleid gebaseerde verkeers selectie uitgeschakeld, maar blijft het IPsec/IKE-beleid ongewijzigd:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>Volgende stappen
Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Een virtuele machine maken](../virtual-machines/windows/quick-create-portal.md) voor de stappen.

Lees ook [IPSec/IKE-beleid configureren voor S2S VPN-of vnet-naar-vnet-verbindingen](vpn-gateway-ipsecikepolicy-rm-powershell.md) voor meer informatie over aangepaste IPSec/IKE-beleids regels.
