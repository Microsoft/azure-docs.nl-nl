---
title: 'BGP configureren op Azure VPN Gateway: CLI'
description: Dit artikel begeleidt u bij het configureren van BGP met een Azure VPN-gateway met behulp van Azure Resource Manager en CLI.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: a69ce0592b79be0868dd7c15ac054910eee75fc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89393595"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>BGP configureren op een Azure VPN-gateway met behulp van CLI

Dit artikel helpt u BGP in te scha kelen op een cross-premises site-naar-site (S2S) VPN-verbinding en een VNet-naar-VNet-verbinding (dat wil zeggen, een verbinding tussen virtuele netwerken) met behulp van het Azure Resource Manager-implementatie model en Azure CLI.

## <a name="about-bgp"></a>Over BGP

BGP is het standaard routerings protocol dat doorgaans op Internet wordt gebruikt om routerings-en bereik baarheids gegevens tussen twee of meer netwerken te uitwisselen. Met BGP kunnen VPN-gateways en uw on-premises VPN-apparaten, BGP-peers of neighbors, worden uitgewisseld naar Exchange-routes. De routes informeren beide gateways over de beschik baarheid en het bereik voor voor voegsels om door de betrokken gateways of routers te gaan. Met BGP kan ook transit routering tussen meerdere netwerken worden ingeschakeld door de routes die een BGP-gateway van een BGP-peer heeft door gegeven, toe te wijzen aan alle andere BGP-peers.

Zie [overzicht van BGP met Azure VPN-gateways](vpn-gateway-bgp-overview.md)voor meer informatie over de voor delen van BGP en om inzicht te krijgen in de technische vereisten en overwegingen voor het gebruik van BGP.

Dit artikel helpt u bij de volgende taken:

* [BGP inschakelen voor uw VPN-gateway](#enablebgp) (vereist)

  U kunt vervolgens een van de volgende secties volt ooien of beide:

* [Een cross-premises verbinding tot stand brengen met BGP](#crossprembgp)
* [Een VNet-naar-VNet-verbinding tot stand brengen met BGP](#v2vbgp)

Elk van deze drie secties vormt een basis bouwsteen voor het inschakelen van BGP in uw netwerk verbinding. Als u alle drie de secties voltooit, bouwt u de topologie op zoals weer gegeven in het volgende diagram:

![BGP-topologie](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

U kunt deze secties combi neren om een complex multihop Transit netwerk te bouwen dat aan uw behoeften voldoet.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>BGP inschakelen voor uw VPN-gateway

Deze sectie is vereist voordat u een van de stappen in de andere twee configuratie secties uitvoert. Met de volgende configuratie stappen worden de BGP-para meters van de Azure VPN-gateway ingesteld, zoals weer gegeven in het volgende diagram:

![BGP-gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Voordat u begint

Installeer de nieuwste versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI installeren](/cli/azure/install-azure-cli) en [Aan de slag met Azure CLI](/cli/azure/get-started-with-azure-cli) voor meer informatie over de CLI-opdrachten.

### <a name="step-1-create-and-configure-testvnet1"></a>Stap 1: TestVNet1 maken en configureren

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Maak verbinding met uw abonnement

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. een resource groep maken

In het volgende voor beeld wordt een resource groep met de naam TestRG1 gemaakt op de locatie ' oostelijke '. Als u al een resource groep hebt in de regio waarin u het virtuele netwerk wilt maken, kunt u dat in plaats daarvan gebruiken.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 maken

In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam TestVNet1 en drie subnetten: GatewaySubnet, front-end en back-end. Wanneer u waarden vervangt, is het belang rijk dat u het subnet van de gateway altijd een naam GatewaySubnet. Als u een andere naam kiest, mislukt het maken van de gateway.

Met de eerste opdracht maakt u de front-end-adres ruimte en het FrontEnd-subnet. Met de tweede opdracht maakt u een extra adres ruimte voor het back-end-subnet. Met de derde en vierde opdracht maakt u het back-end-subnet en de GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Stap 2: de VPN-gateway maken voor TestVNet1 met BGP-para meters

#### <a name="1-create-the-public-ip-address"></a>1. het open bare IP-adres maken

Vraag een openbaar IP-adres aan. Het open bare IP-adres wordt toegewezen aan de VPN-gateway die u voor uw virtuele netwerk hebt gemaakt.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Maak de VPN-gateway met het AS-nummer

Maak de gateway van het virtuele netwerk voor TestVNet1. BGP vereist een Route-Based VPN-gateway. U hebt ook de extra para meter nodig `-Asn` om het autonome systeem nummer (ASN) voor TestVNet1 in te stellen. Het maken van een gateway kan enige tijd duren (45 minuten of langer). 

Als u deze opdracht uitvoert met behulp van de `--no-wait` -para meter, worden er geen feedback of uitvoer weer gegeven. `--no-wait`Met de para meter kan de gateway op de achtergrond worden gemaakt. Dit betekent niet dat de VPN-gateway onmiddellijk wordt gemaakt.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. het IP-adres van de Azure BGP-peer verkrijgen

Nadat de gateway is gemaakt, moet u het IP-adres van de BGP-peer verkrijgen op de Azure VPN-gateway. Dit adres is nodig voor het configureren van de VPN-gateway als een BGP-peer voor uw on-premises VPN-apparaten.

Voer de volgende opdracht uit en controleer de `bgpSettings` sectie aan de bovenkant van de uitvoer:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Nadat de gateway is gemaakt, kunt u deze gateway gebruiken om een cross-premises verbinding of een VNet-naar-VNet-verbinding met BGP tot stand te brengen.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>Een cross-premises verbinding tot stand brengen met BGP

Als u een cross-premises verbinding tot stand wilt brengen, moet u een lokale netwerk gateway maken om uw on-premises VPN-apparaat aan te duiden. Vervolgens verbindt u de Azure VPN-gateway met de lokale netwerk gateway. Hoewel deze stappen vergelijkbaar zijn met het maken van andere verbindingen, bevatten ze de extra eigenschappen die vereist zijn voor het opgeven van de BGP-configuratie parameters.

![BGP voor cross-premises](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Stap 1: de lokale netwerk gateway maken en configureren

Deze oefening gaat verder met het bouwen van de configuratie die in het diagram wordt weer gegeven. Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken. Wanneer u met lokale netwerk gateways werkt, houdt u rekening met het volgende:

* De lokale netwerk gateway kan zich in dezelfde locatie en resource groep bevindt als de VPN-gateway, of kan zich op een andere locatie en resource groep bevindt. In dit voor beeld worden de gateways in verschillende resource groepen op verschillende locaties weer gegeven.
* Het minimale voor voegsel dat u moet declareren voor de lokale netwerk gateway is het hostadres van het IP-adres van uw BGP-peer op uw VPN-apparaat. In dit geval is het een/32-voor voegsel van 10.51.255.254/32.
* Als herinnering moet u verschillende BGP-Asn's gebruiken tussen uw on-premises netwerken en het virtuele Azure-netwerk. Als ze hetzelfde zijn, moet u uw VNet-ASN wijzigen als uw on-premises VPN-apparaten het ASN al gebruiken voor peer met andere BGP-neighbors.

Voordat u doorgaat, moet u ervoor zorgen dat u het gedeelte [BGP inschakelen voor uw VPN-gateway](#enablebgp) in deze oefening hebt voltooid en dat u nog steeds bent verbonden met abonnement 1. In dit voor beeld maakt u een nieuwe resource groep. Let ook op de twee extra para meters voor de gateway van het lokale netwerk: `Asn` en `BgpPeerAddress` .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Stap 2: de VNet-gateway en de lokale netwerk gateway verbinden

In deze stap maakt u de verbinding van TestVNet1 naar site5. U moet de `--enable-bgp` para meter opgeven om BGP in te scha kelen voor deze verbinding. 

In dit voor beeld bevinden de gateway van het virtuele netwerk en de lokale netwerk gateway zich in verschillende resource groepen. Wanneer de gateways zich in verschillende resource groepen bevinden, moet u de volledige Resource-ID van de twee gateways opgeven voor het instellen van een verbinding tussen de virtuele netwerken.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Haal de resource-ID van VNet1GW op

Gebruik de uitvoer van de volgende opdracht om de resource-ID voor VNet1GW op te halen:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Zoek in de uitvoer de `"id":` regel. U hebt de waarden binnen de aanhalings tekens nodig om de verbinding in de volgende sectie te maken.

Voorbeelduitvoer:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Kopieer de waarden na `"id":` naar een tekst editor, zoals Klad blok, zodat u ze eenvoudig kunt plakken tijdens het maken van de verbinding. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Haal de resource-ID van site5 op

Gebruik de volgende opdracht om de resource-ID van site5 op te halen uit de uitvoer:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Maak de TestVNet1-naar-Site5-verbinding

In deze stap maakt u de verbinding van TestVNet1 naar site5. Zoals eerder is besproken, is het mogelijk om zowel BGP-als niet-BGP-verbindingen te hebben voor dezelfde Azure VPN-gateway. Tenzij BGP is ingeschakeld in de eigenschap Connection, wordt BGP voor deze verbinding niet ingeschakeld door Azure, zelfs als de BGP-para meters al zijn geconfigureerd op beide gateways. Vervang de abonnements-Id's door uw eigen abonnement.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

In het volgende voor beeld ziet u de para meters die moeten worden opgegeven in het gedeelte BGP-configuratie van uw on-premises VPN-apparaat:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

De verbinding moet na enkele minuten tot stand zijn gebracht. De BGP-peering-sessie wordt gestart nadat de IPsec-verbinding tot stand is gebracht.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Een VNet-naar-VNet-verbinding tot stand brengen met BGP

In deze sectie wordt een VNet-naar-VNet-verbinding met BGP toegevoegd, zoals in het volgende diagram wordt weer gegeven: 

![BGP voor VNet-naar-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

De volgende instructies worden door lopen van de stappen in de voor gaande secties. Als u TestVNet1 en de VPN-gateway met BGP wilt maken en configureren, moet u de sectie [BGP inschakelen voor uw VPN-gateway](#enablebgp) volt ooien.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Stap 1: TestVNet2 en de VPN-gateway maken

Het is belang rijk om ervoor te zorgen dat de IP-adres ruimte van het nieuwe virtuele netwerk, TestVNet2, geen van uw VNet-bereiken overlapt.

In dit voor beeld maken de virtuele netwerken deel uit van hetzelfde abonnement. U kunt VNet-naar-VNet-verbindingen tussen verschillende abonnementen instellen. Zie [een vnet-naar-VNet-verbinding configureren](vpn-gateway-howto-vnet-vnet-cli.md)voor meer informatie. Zorg ervoor dat u toevoegt `-EnableBgp $True` tijdens het maken van de verbindingen om BGP in te scha kelen.

#### <a name="1-create-a-new-resource-group"></a>1. een nieuwe resource groep maken

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. TestVNet2 maken in de nieuwe resource groep

Met de eerste opdracht maakt u de front-end-adres ruimte en het FrontEnd-subnet. Met de tweede opdracht maakt u een extra adres ruimte voor het back-end-subnet. Met de derde en vierde opdracht maakt u het back-end-subnet en de GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. het open bare IP-adres maken

Vraag een openbaar IP-adres aan. Het open bare IP-adres wordt toegewezen aan de VPN-gateway die u voor uw virtuele netwerk hebt gemaakt.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Maak de VPN-gateway met het AS-nummer

Maak de gateway van het virtuele netwerk voor TestVNet2. U moet de standaard-ASN op uw Azure VPN-gateways negeren. De Asn's voor de verbonden virtuele netwerken moeten verschillend zijn om BGP en transit routering in te scha kelen.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Stap 2: verbinding maken met de TestVNet1-en TestVNet2-gateways

In deze stap maakt u de verbinding van TestVNet1 naar site5. Als u BGP wilt inschakelen voor deze verbinding, moet u de `--enable-bgp` para meter opgeven.

In het volgende voor beeld bevinden de gateway van het virtuele netwerk en de lokale netwerk gateway zich in verschillende resource groepen. Wanneer de gateways zich in verschillende resource groepen bevinden, moet u de volledige Resource-ID van de twee gateways opgeven voor het instellen van een verbinding tussen de virtuele netwerken. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Haal de resource-ID van VNet1GW op 

Haal de resource-ID van VNet1GW op uit de uitvoer van de volgende opdracht:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Haal de resource-ID van VNet2GW op

Haal de resource-ID van VNet2GW op uit de uitvoer van de volgende opdracht:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. de verbindingen maken

Maak de verbinding van TestVNet1 naar TestVNet2 en de verbinding van TestVNet2 naar TestVNet1. Vervang de abonnements-Id's door uw eigen abonnement.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Schakel BGP in voor *beide* verbindingen.
> 
> 

Nadat u deze stappen hebt voltooid, wordt de verbinding in een paar minuten tot stand gebracht. De BGP-peering-sessie wordt uitgevoerd nadat de VNet-naar-VNet-verbinding is voltooid.

## <a name="next-steps"></a>Volgende stappen

Nadat de verbinding is voltooid, kunt u virtuele machines toevoegen aan uw virtuele netwerken. Zie [een virtuele machine maken voor een](../virtual-machines/windows/quick-create-portal.md)overzicht van de stappen.
