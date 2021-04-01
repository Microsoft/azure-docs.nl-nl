---
title: Een zone-redundante virtuele netwerk gateway maken in Azure-beschikbaarheidszones
description: Meer informatie over het implementeren van zone-redundante VPN-gateways en ExpressRoute-gateways in Azure-beschikbaarheidszones.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2eaf1470e2d861ecfc1c1bc96f6040a1c3e0a644
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89425225"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Een zone-redundante virtuele netwerk gateway maken in Azure-beschikbaarheidszones

U kunt VPN-en ExpressRoute-gateways implementeren in Azure-beschikbaarheidszones. Dit zorgt in een virtuele netwerkgateway voor tolerantie, schaalbaarheid en hoge beschikbaarheid. Gateways fysiek en logisch implementeren in Azure-beschikbaarheidszones scheidt gateways binnen een regio, terwijl uw on-premises netwerkconnectiviteit met Azure wordt beschermd tegen fouten op zoneniveau. Zie  [info over zone-redundante virtuele netwerk gateways](about-zone-redundant-vnet-gateways.md) en [over Azure-beschikbaarheidszones](../availability-zones/az-overview.md)voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-declare-your-variables"></a><a name="variables"></a>1. Declareer de variabelen

Declareer de waarden die u wilt gebruiken. Gebruik het volgende voorbeeld, en vervang zo nodig de waarden door uw eigen waarden. Als u uw Power shell/Cloud Shell-sessie op een wille keurig moment tijdens de oefening sluit, kopieert en plakt u de waarden opnieuw om de variabelen opnieuw te declareren. Wanneer u een locatie opgeeft, controleert u of de regio die u opgeeft, wordt ondersteund. Raadpleeg de [Veelgestelde vragen](#faq)voor meer informatie.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="2-create-the-virtual-network"></a><a name="configure"></a>2. het virtuele netwerk maken

Een resourcegroep maken.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Maak een virtueel netwerk.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="3-add-the-gateway-subnet"></a><a name="gwsub"></a>3. Voeg het gateway-subnet toe

Het gateway-subnet bevat de gereserveerde IP-adressen die door de Gateway Services van het virtuele netwerk worden gebruikt. Gebruik de volgende voor beelden om een gateway-subnet toe te voegen en in te stellen:

Voeg het gatewaysubnet toe.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Stel de configuratie van het subnet van de gateway in voor het virtuele netwerk.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="4-request-a-public-ip-address"></a><a name="publicip"></a>4. een openbaar IP-adres aanvragen
 
In deze stap kiest u de instructies die van toepassing zijn op de gateway die u wilt maken. De selectie van zones voor het implementeren van de gateways is afhankelijk van de zones die zijn opgegeven voor het open bare IP-adres.

### <a name="for-zone-redundant-gateways"></a><a name="ipzoneredundant"></a>Voor zone-redundante gateways

Vraag een openbaar IP-adres aan met een **standaard** PUBLICIPADDRESS-SKU en geef geen zone op. In dit geval is het standaard open bare IP-adres een zone-redundant openbaar IP.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="for-zonal-gateways"></a><a name="ipzonalgw"></a>Voor zonegebonden-gateways

Vraag een openbaar IP-adres aan met een **standaard** PUBLICIPADDRESS-SKU. Geef de zone op (1, 2 of 3). Alle gateway-exemplaren worden in deze zone geïmplementeerd.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="for-regional-gateways"></a><a name="ipregionalgw"></a>Voor regionale gateways

Vraag een openbaar IP-adres aan met een **basis** -PUBLICIPADDRESS-SKU. In dit geval wordt de gateway geïmplementeerd als een regionale gateway en heeft deze geen zone-redundantie ingebouwd in de gateway. De gateway-exemplaren worden respectievelijk in zones gemaakt.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="5-create-the-ip-configuration"></a><a name="gwipconfig"></a>5. de IP-configuratie maken

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="6-create-the-gateway"></a><a name="gwconfig"></a>6. de gateway maken

Maak de gateway van het virtuele netwerk.

### <a name="for-expressroute"></a>Voor ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>Voor VPN Gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a><a name="faq"></a>Veelgestelde vragen

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Wat verandert er wanneer ik deze nieuwe Sku's Implementeer?

Vanuit uw perspectief kunt u uw gateways implementeren met zone-redundantie. Dit betekent dat alle exemplaren van de gateways worden geïmplementeerd over Azure-beschikbaarheidszones en elke beschikbaarheids zone is een ander fout-en update domein. Dit maakt het mogelijk dat uw gateways betrouwbaarder, beschikbaar en robuust zijn voor zone storingen.

### <a name="can-i-use-the-azure-portal"></a>Kan ik de Azure Portal gebruiken?

Ja, u kunt de Azure Portal gebruiken om de nieuwe Sku's te implementeren. Deze nieuwe Sku's worden echter alleen in die Azure-regio's met Azure-beschikbaarheidszones weer geven.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Welke regio's zijn er beschikbaar voor mij om de nieuwe Sku's te gebruiken?

Zie [Beschikbaarheidszones](../availability-zones/az-region.md) voor de meest recente lijst met beschik bare regio's.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Kan ik mijn bestaande virtuele netwerk gateways wijzigen/migreren/upgraden naar zone-redundante of zonegebonden-gateways?

Het migreren van uw bestaande virtuele netwerk gateways naar zone-redundante of zonegebonden-gateways wordt momenteel niet ondersteund. U kunt echter uw bestaande gateway verwijderen en een zone-redundante of zonegebonden-Gateway opnieuw maken.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Kan ik zowel VPN-als Express route gateways in hetzelfde virtuele netwerk implementeren?

Zowel VPN-als Express route-gateways in hetzelfde virtuele netwerk worden ondersteund. U moet echter een/27 IP-adres bereik reserveren voor het gateway-subnet.
