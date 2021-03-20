---
title: 'Azure ExpressRoute: S2S VPN configureren via micro soft-peering'
description: Configureer IPsec/IKE-connectiviteit met Azure via een ExpressRoute micro soft peering-circuit met behulp van een site-naar-site-VPN-gateway.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 366f27a0e2a22e9aa10dda20e105bf644255bdd4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89393136"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Een site-naar-site-VPN configureren via ExpressRoute micro soft-peering

Dit artikel helpt u bij het configureren van beveiligde versleutelde connectiviteit tussen uw on-premises netwerk en uw virtuele Azure-netwerken (VNets) via een ExpressRoute-particuliere verbinding. U kunt micro soft-peering gebruiken om een site-naar-site IPsec/IKE-VPN-tunnel te maken tussen de geselecteerde on-premises netwerken en Azure VNets. Het configureren van een beveiligde tunnel via ExpressRoute maakt het mogelijk om gegevens uitwisseling met vertrouwelijkheid, anti-replay, authenticiteit en integriteit te waarborgen.

>[!NOTE]
>Wanneer u site-naar-site-VPN via micro soft-peering instelt, worden er kosten in rekening gebracht voor de VPN-gateway en VPN-uitgaand verkeer. Zie [VPN gateway prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway)voor meer informatie.
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>Architectuur


  ![connectiviteits overzicht](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Voor hoge Beschik baarheid en redundantie kunt u meerdere tunnels configureren op basis van de twee MSEE-PE-paren van een ExpressRoute-circuit en taak verdeling tussen de tunnels inschakelen.

  ![opties voor hoge Beschik baarheid](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

VPN-tunnels via micro soft-peering kunnen worden beëindigd met behulp van VPN gateway of met behulp van een geschikt virtueel netwerk apparaat (NVA) dat beschikbaar is via Azure Marketplace. U kunt routes statisch of dynamisch via de versleutelde tunnels uitwisselen zonder dat de route-uitwisseling naar de onderliggende micro soft-peering wordt weer gegeven. In de voor beelden in dit artikel wordt BGP (verschillend van de BGP-sessie die is gebruikt om de micro soft-peering te maken) gebruikt voor het dynamisch uitwisselen van voor voegsels voor de versleutelde tunnels.

>[!IMPORTANT]
>Voor de on-premises kant wordt micro soft-peering meestal beëindigd op de DMZ en de persoonlijke peering wordt beëindigd op de basis netwerk zone. De twee zones zouden worden gescheiden met firewalls. Als u micro soft-peering alleen wilt configureren voor het inschakelen van beveiligde tunneling via ExpressRoute, moet u ervoor zorgen dat u alleen de open bare Ip's van belang die worden geadverteerd via micro soft-peering, kunt filteren.
>
>

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

1. Configureer micro soft-peering voor uw ExpressRoute-circuit.
2. Adverteer geselecteerde Azure regionale open bare voor voegsels naar uw on-premises netwerk via micro soft-peering.
3. Een VPN-gateway configureren en IPsec-tunnels tot stand brengen
4. Configureer het on-premises VPN-apparaat.
5. Maak de site-naar-site-IPsec/IKE-verbinding.
6. Beschrijving Firewalls/filtering configureren op het on-premises VPN-apparaat.
7. Test en valideer de IPsec-communicatie via het ExpressRoute-circuit.

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1. micro soft-peering configureren

Als u een site-naar-site-VPN-verbinding wilt configureren via ExpressRoute, moet u gebruikmaken van ExpressRoute micro soft-peering.

* Als u een nieuw ExpressRoute-circuit wilt configureren, begint u met het artikel [ExpressRoute vereisten](expressroute-prerequisites.md) en vervolgens [maakt en wijzigt u een ExpressRoute-circuit](expressroute-howto-circuit-arm.md).

* Als u al een ExpressRoute-circuit hebt, maar geen micro soft-peering hebt geconfigureerd, configureert u micro soft-peering met het artikel [maken en wijzigen van peering voor een ExpressRoute-circuit](expressroute-howto-routing-arm.md#msft) .

Zodra u het circuit en micro soft-peering hebt geconfigureerd, kunt u het eenvoudig weer geven met behulp van de pagina **overzicht** in de Azure Portal.

![schakelaar](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2. route filters configureren

Via een routefilter kunt u services identificeren die u wilt gebruiken via Microsoft-peering op uw ExpressRoute-circuit. Het is in feite een lijst met toegestane waarden voor de BGP-community. 

![route filter](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

In dit voor beeld is de implementatie alleen in de regio *Azure West US 2* . Er wordt een regel voor route filter toegevoegd zodat alleen de advertentie van de regionale voor voegsels van Azure West 2 regionaal wordt toegestaan. deze heeft de BGP Community-waarde *12076:51026*. U geeft de regionale voor voegsels op die u wilt toestaan door **regel beheren** te selecteren.

Binnen het route filter moet u ook de ExpressRoute-circuits kiezen waarvoor het route filter van toepassing is. U kunt de ExpressRoute-circuits kiezen door **circuit toevoegen** te selecteren. In de vorige afbeelding is het route filter gekoppeld aan het voor beeld-ExpressRoute-circuit.

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2,1 het route filter configureren

Configureer een route filter. Zie voor stappen [route filters configureren voor micro soft-peering](how-to-routefilter-portal.md).

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2,2 BGP-routes verifiëren

Wanneer u micro soft-peering hebt gemaakt via uw ExpressRoute-circuit en een route filter met het circuit hebt gekoppeld, kunt u de BGP-routes controleren die zijn ontvangen van Msee's op de PE-apparaten die peering hebben met de Msee's. De verificatie opdracht varieert, afhankelijk van het besturings systeem van uw PE-apparaten.

#### <a name="cisco-examples"></a>Cisco-voor beelden

In dit voor beeld wordt een Cisco IOS-XE-opdracht gebruikt. In het voor beeld wordt een VRF-exemplaar (Virtual Routing and Forwarding) gebruikt om het peering-verkeer te isoleren.

```
show ip bgp vpnv4 vrf 10 summary
```

In de volgende gedeeltelijke uitvoer ziet u dat er 68-voor voegsels zijn ontvangen van de neighbor \* . 243.229.34 met ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Gebruik het volgende voor beeld om de lijst met voor voegsels te zien die zijn ontvangen van de neighbor:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Als u wilt controleren of u de juiste set voor voegsels ontvangt, kunt u de verificatie afkruisen. Met de volgende Azure PowerShell opdracht uitvoer worden de voor voegsels vermeld die zijn geadverteerd via micro soft-peering voor elk van de services en voor elk van de Azure-regio's:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3. de VPN-gateway en de IPsec-tunnels configureren

In deze sectie worden IPsec VPN-tunnels gemaakt tussen de Azure VPN-gateway en het on-premises VPN-apparaat. In de voor beelden worden CSR1000-VPN-apparaten (Cisco Cloud service router) gebruikt.

In het volgende diagram ziet u de IPsec VPN-tunnels tussen on-premises VPN-apparaat 1 en het exemplaar paar van de Azure VPN-gateway. De twee IPsec VPN-tunnels die tot stand zijn gebracht tussen het on-premises VPN-apparaat 2 en het Azure VPN gateway-exemplaar paar worden niet weer gegeven in het diagram en de configuratie details worden niet vermeld. Als er echter extra VPN-tunnels zijn, wordt de maximale Beschik baarheid verbeterd.

  ![VPN-tunnels](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Via het IPsec-tunnel paar wordt er een eBGP-sessie tot stand gebracht voor het uitwisselen van particuliere netwerk routes. In het volgende diagram ziet u de eBGP-sessie die is ingesteld via het IPsec-tunnel paar:

  ![eBGP-sessies via tunnel paar](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Het volgende diagram toont het overzicht van het voorbeeld netwerk:

  ![voor beeld netwerk](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Over de Azure Resource Manager sjabloon voorbeelden

In de voor beelden worden de VPN-gateway en de IPsec-tunnel afsluitingen geconfigureerd met behulp van een Azure Resource Manager sjabloon. Zie [inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-syntax.md)als u geen ervaring hebt met het gebruik van Resource Manager-sjablonen of als u meer wilt weten over de basis principes van de Resource Manager-sjabloon. Met de sjabloon in deze sectie maakt u een ontwikkel Azure-omgeving (VNet). Als u echter een bestaand VNet hebt, kunt u ernaar verwijzen in de sjabloon. Zie [een site-naar-site-verbinding maken](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)als u niet bekend bent met VPN gateway IPSec/IKE site-naar-site-configuraties.

>[!NOTE]
>U hoeft Azure Resource Manager sjablonen niet te gebruiken om deze configuratie te kunnen maken. U kunt deze configuratie maken met behulp van de Azure Portal of Power shell.
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3,1 Declareer de variabelen

In dit voor beeld komen de variabelen declaraties overeen met het voorbeeld netwerk. Wanneer u variabelen declareert, wijzigt u deze sectie zodat deze overeenkomt met uw omgeving.

* De variabele **localAddressPrefix** is een matrix van on-PREMISES IP-adressen om de IPSec-tunnels te beëindigen.
* De **gatewaySku** bepaalt de VPN-door voer. Zie [VPN gateway-configuratie-instellingen](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)voor meer informatie over GatewaySku en vpnType. Zie [VPN gateway prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway)voor prijzen.
* Stel **vpnType** in op **RouteBased**.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3,2 virtueel netwerk (VNet) maken

Als u een bestaand VNet koppelt aan de VPN-tunnels, kunt u deze stap overs Laan.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3,3 open bare IP-adressen toewijzen aan exemplaren van VPN-gateway
 
Wijs een openbaar IP-adres toe voor elk exemplaar van een VPN-gateway.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3,4 opgeven van on-premises VPN-tunnel afsluiting (lokale netwerk gateway)

De on-premises VPN-apparaten worden aangeduid als de **lokale netwerk gateway**. In het volgende JSON-fragment worden ook externe BGP-peer gegevens opgegeven:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3,5 de VPN-gateway maken

In deze sectie van de sjabloon wordt de VPN-gateway geconfigureerd met de vereiste instellingen voor een actief/actief-configuratie. Houd de volgende vereisten in acht:

* Maak de VPN-gateway met een **RouteBased** -VpnType. Deze instelling is verplicht als u BGP-route ring wilt inschakelen tussen de VPN-gateway en de VPN on-premises.
* Als u VPN-tunnels wilt maken tussen de twee exemplaren van de VPN-gateway en een gegeven on-premises apparaat in de modus actief-actief, wordt de para meter **' activeActive '** ingesteld op ' **True** ' in de Resource Manager-sjabloon. Zie connectiviteit met hoge Beschik baarheid [VPN-gateway](../vpn-gateway/vpn-gateway-highlyavailable.md)voor meer informatie over Maxi maal beschik bare VPN-gateways.
* Als u eBGP-sessies tussen de VPN-tunnels wilt configureren, moet u twee verschillende Asn's aan beide zijden opgeven. Het is raadzaam om persoonlijke ASN-getallen op te geven. Zie [overzicht van BGP-en Azure VPN-gateways](../vpn-gateway/vpn-gateway-bgp-overview.md)voor meer informatie.

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3,6 de IPsec-tunnels tot stand brengen

De laatste actie van het script maakt IPsec-tunnels tussen de Azure VPN-gateway en het on-premises VPN-apparaat.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4. het on-premises VPN-apparaat configureren

De Azure VPN-gateway is compatibel met veel VPN-apparaten van verschillende leveranciers. Zie [over VPN-apparaten](../vpn-gateway/vpn-gateway-about-vpn-devices.md)voor informatie over de configuratie en de apparaten die zijn gevalideerd voor gebruik met de VPN-gateway.

Bij het configureren van uw VPN-apparaat hebt u de volgende items nodig:

* Een gedeelde sleutel. Dit is dezelfde gedeelde sleutel die u opgeeft wanneer u uw site-naar-site-VPN-verbinding maakt. In de voor beelden wordt gebruikgemaakt van een basis gedeelde sleutel. We raden u aan een complexere sleutel te genereren.
* Het open bare IP-adres van uw VPN-gateway. U kunt het openbare IP-adres weergeven met behulp van Azure Portal, PowerShell of de CLI. Navigeer naar Virtuele netwerkgateways en klik op de naam van uw VPN-gateway om het openbare IP-adres dat gebruikmaakt van Azure Portal te achterhalen.

Doorgaans worden eBGP-peers rechtstreeks verbonden (vaak via een WAN-verbinding). Wanneer u echter eBGP via IPsec VPN-tunnels configureert via ExpressRoute micro soft-peering, zijn er meerdere routerings domeinen tussen de eBGP-peers. Gebruik de **ebgp-multihop** opdracht voor het instellen van de ebgp neighbor-relatie tussen de twee niet rechtstreeks verbonden peers. Het gehele getal dat volgt ebgp-multihop opdracht geeft de TTL-waarde in de BGP-pakketten. De opdracht **maximum aantal paden eibgp 2** maakt taak verdeling van verkeer tussen de twee BGP-paden mogelijk.

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Cisco CSR1000-voor beeld

In het volgende voor beeld ziet u de configuratie van Cisco CSR1000 in een Hyper-V virtuele machine als een on-premises VPN-apparaat:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5. filteren van VPN-apparaten en firewalls configureren (optioneel)

Configureer uw firewall en filter op basis van uw vereisten.

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6. de IPsec-tunnel testen en valideren

De status van IPsec-tunnels kan worden gecontroleerd op de Azure VPN-gateway met behulp van Power shell-opdrachten:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Voorbeelduitvoer:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Gebruik het volgende voor beeld om de status van de tunnels op de exemplaren van de Azure VPN-gateway onafhankelijk te controleren:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Voorbeelduitvoer:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

U kunt ook de tunnel status op uw on-premises VPN-apparaat controleren.

Cisco CSR1000-voor beeld:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Voorbeelduitvoer:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

Het regel Protocol van de virtuele tunnel interface (VTI) wordt pas gewijzigd in ' up ' tot IKE fase 2 is voltooid. Met de volgende opdracht wordt de beveiligings koppeling gecontroleerd:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verify-end-to-end-connectivity-between-the-inside-network-on-premises-and-the-azure-vnet"></a><a name="verifye2e"></a>End-to-end-connectiviteit tussen het lokale netwerk en het Azure-VNet verifiëren

Als de IPsec-tunnels actief zijn en de statische routes correct zijn ingesteld, kunt u het IP-adres van de externe BGP-peer pingen:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>De BGP-sessies via IPsec verifiëren

Controleer de status van de BGP-peer op de Azure VPN-gateway:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Voorbeelduitvoer:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Als u de lijst met netwerk voorvoegsels wilt controleren die via eBGP van de VPN-concentrator on-premises worden ontvangen, kunt u filteren op kenmerk ' Origin ':

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

In het voorbeeld uitvoer is het ASN 65010 het autonome BGP-systeem nummer in het on-premises VPN-netwerk.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

De lijst met aangekondigde routes weer geven:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Voorbeelduitvoer:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Voor beeld voor de on-premises Cisco CSR1000:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

De lijst met netwerken die zijn geadverteerd vanaf de on-premises Cisco-CSR1000 naar de Azure VPN-gateway, kan worden weer gegeven met de volgende opdracht:

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Volgende stappen

* [Netwerkprestatiemeter configureren voor ExpressRoute](how-to-npm.md)

* [Een site-naar-site-verbinding met een VNet toevoegen met een bestaande VPN-gateway verbinding](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
