---
title: 'Uw on-premises netwerk verbinden met een virtueel Azure-netwerk: site-naar-site-VPN: Power shell'
description: Maak een IPsec-site-naar-site-VPN Gateway verbinding van uw on-premises netwerk met een virtueel Azure-netwerk via het open bare Internet met behulp van Power shell.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 1488aa6f48c05a8c2dfa2c6162c1bd1df35d4f58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380481"
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Een VNet met een site-naar-site-VPN-verbinding maken met PowerShell

In dit artikel leest u hoe u PowerShell gebruikt om een site-naar-site-VPN-gatewayverbinding te maken vanaf uw lokale netwerk naar het VNet. De stappen in dit artikel zijn van toepassing op het Resource Manager-implementatiemodel. U kunt deze configuratie ook maken met een ander implementatiehulpprogramma of een ander implementatiemodel door in de volgende lijst een andere optie te selecteren:

> [!div class="op_single_selector"]
> * [Azure-portal](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (klassiek)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Een site-naar-site-VPN-gatewayverbinding wordt gebruikt om een on-premises netwerk via een IPsec-/IKE-VPN-tunnel (IKEv1 of IKEv2) te verbinden met een virtueel Azure-netwerk. Voor dit type verbinding moet er on-premises een VPN-apparaat aanwezig zijn waaraan een extern openbaar IP-adres is toegewezen. Zie [Overzicht van VPN Gateway](vpn-gateway-about-vpngateways.md) voor meer informatie over VPN-gateways.

![Diagram: cross-premises site-naar-site-VPN-gatewayverbinding](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

Controleer voordat u met de configuratie begint of u aan de volgende criteria hebt voldaan:

* U hebt een compatibel VPN-apparaat nodig en iemand die dit kan configureren. Zie [Over VPN-apparaten](vpn-gateway-about-vpn-devices.md) voor meer informatie over compatibele VPN-apparaten en -apparaatconfiguratie.
* Controleer of u een extern gericht openbaar IPv4-adres voor het VPN-apparaat hebt.
* Als u de IP-adresbereiken in uw on-premises netwerkconfiguratie niet kent, moet u contact opnemen met iemand die u hierbij kan helpen en de benodigde gegevens kan verstrekken. Wanneer u deze configuratie maakt, moet u de IP-adresbereikvoorvoegsels opgeven die Azure naar uw on-premises locatie doorstuurt. Geen van de subnetten van uw on-premises netwerk kan overlappen met de virtuele subnetten waarmee u verbinding wilt maken.

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Voorbeeldwaarden

In de voorbeelden in dit artikel worden de volgende waarden gebruikt. U kunt deze waarden gebruiken om een testomgeving te maken of ze raadplegen om meer inzicht te krijgen in de voorbeelden in dit artikel.

```
#Example values

VnetName                = VNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.1.0.0/16 
SubnetName              = Frontend 
Subnet                  = 10.1.0.0/24 
GatewaySubnet           = 10.1.255.0/27
LocalNetworkGatewayName = Site1
LNG Public IP           = <On-premises VPN device IP address> 
Local Address Prefixes  = 10.101.0.0/24, 10.101.1.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWPIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite1

```

## <a name="1-create-a-virtual-network-and-a-gateway-subnet"></a><a name="VNet"></a>1. een virtueel netwerk en een gateway-subnet maken

Als u nog geen virtueel netwerk hebt, maakt u er een. Controleer bij het maken van een virtueel netwerk of de adresruimten die u opgeeft, niet overlappen met adresruimten in uw on-premises netwerk. 

>[!NOTE]
>U dient eerst met uw on-premises netwerkbeheerder een IP-adresbereik te reserveren dat u specifiek voor dit virtuele netwerk kunt gebruiken, voordat dit VNet verbinding met een on-premises locatie kan maken. Als er een dubbel adresbereik bestaat aan beide zijden van de VPN-verbinding, wordt verkeer mogelijk niet zoals verwacht gerouteerd. Als u dit VNet met een ander VNet wilt verbinden, kan de adresruimte niet met het andere VNet overlappen. Plan daarom uw netwerkconfiguratie zorgvuldig.
>
>

### <a name="about-the-gateway-subnet"></a>Over het gatewaysubnet

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="create-a-virtual-network-and-a-gateway-subnet"></a><a name="vnet"></a>Een virtueel netwerk en een gatewaysubnet maken

In dit voorbeeld worden een virtueel netwerk en een gatewaysubnet gemaakt. Zie [Een gatewaysubnet toevoegen aan een virtueel netwerk dat u al hebt gemaakt](#gatewaysubnet) als u al een virtueel netwerk hebt waaraan u een gatewaysubnet wilt toevoegen.

Een resourcegroep maken:

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location 'East US'
```

Maak uw virtuele netwerk.

1. Stel de variabelen in.

   ```azurepowershell-interactive
   $subnet1 = New-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27
   $subnet2 = New-AzVirtualNetworkSubnetConfig -Name 'Frontend' -AddressPrefix 10.1.0.0/24
   ```
2. Maak het VNet.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1 `
   -Location 'East US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet1, $subnet2
   ```

### <a name="to-add-a-gateway-subnet-to-a-virtual-network-you-have-already-created"></a><a name="gatewaysubnet"></a>Een gatewaysubnet toevoegen aan een virtueel netwerk dat u al hebt gemaakt

Gebruik de stappen in deze sectie als u al een virtueel netwerk hebt, maar een gatewaysubnet wilt toevoegen.

1. Stel de variabelen in.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
   ```
2. Maak het gatewaysubnet.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
   ```
3. Stel de configuratie in.

   ```azurepowershell-interactive
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```

## <a name="2-create-the-local-network-gateway"></a>2. <a name="localnet"></a> de lokale netwerk gateway maken

De lokale netwerk gateway (LNG) verwijst doorgaans naar uw on-premises locatie. Dit is niet hetzelfde als een virtuele netwerk gateway. U geeft de site een naam waarmee Azure hiernaar kan verwijzen en geeft vervolgens het IP-adres op van het on-premises VPN-apparaat waarmee u verbinding maakt. U geeft ook de IP-adresvoorvoegsels op die via de VPN-gateway worden doorgestuurd naar het VPN-apparaat. De adresvoorvoegsels die u opgeeft, zijn de voorvoegsels die zich in uw on-premises netwerk bevinden. Als uw on-premises netwerk verandert, kunt u de voorvoegsels eenvoudig bijwerken.

Gebruik de volgende waarden:

* De *GatewayIPAddress* is het IP-adres van uw on-PREMISES VPN-apparaat.
* *AddressPrefix* is uw on-premises adresruimte.

Ga als volgt te werk als u een lokale netwerkgateway met één adresvoorvoegsel wilt toevoegen:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.101.0.0/24'
  ```

Ga als volgt te werk als u een lokale netwerkgateway met meerdere adresvoorvoegsels wilt toevoegen:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```

IP-adresvoorvoegsels wijzigen voor uw lokale netwerkgateway:

Soms veranderen de voorvoegsels voor uw lokale netwerkgateway. De stappen waarmee u de IP-adresvoorvoegsels moet wijzigen, zijn afhankelijk van het feit of u een VPN-gatewayverbinding hebt gemaakt. Raadpleeg de sectie [Adresvoorvoegsels voor een lokale netwerkgateway wijzigen](#modify) van dit artikel.

## <a name="3-request-a-public-ip-address"></a><a name="PublicIP"></a>3. een openbaar IP-adres aanvragen

Een VPN Gateway moet een openbaar IP-adres hebben. U vraagt eerst de resource van het IP-adres aan en verwijst hier vervolgens naar bij het maken van uw virtuele netwerkgateway. Het IP-adres wordt dynamisch aan de resource toegewezen wanneer de VPN Gateway wordt gemaakt. 

VPN Gateway ondersteunt momenteel alleen *dynamische* toewijzing van openbare IP-adressen. U kunt geen toewijzing van een statisch openbaar IP-adres aanvragen. Dit betekent echter niet dat het IP-adres wordt gewijzigd nadat het aan uw VPN-gateway is toegewezen. Het openbare IP-adres verandert alleen wanneer de gateway wordt verwijderd en opnieuw wordt gemaakt. Het verandert niet wanneer de grootte van uw VPN Gateway verandert, wanneer deze gateway opnieuw wordt ingesteld of wanneer andere interne onderhoudswerkzaamheden of upgrades worden uitgevoerd.

Vraag een openbaar IP-adres aan dat wordt toegewezen aan de VPN Gateway van uw virtuele netwerk.

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="4-create-the-gateway-ip-addressing-configuration"></a><a name="GatewayIPConfig"></a>4. de gateway-IP-adres configuratie maken

De gateway configuratie definieert het subnet (het ' GatewaySubnet ') en het open bare IP-adres dat moet worden gebruikt. Gebruik het volgende voorbeeld om de gatewayconfiguratie te maken:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="5-create-the-vpn-gateway"></a><a name="CreateGateway"></a>5. de VPN-gateway maken

Maak de VPN-gateway van het virtuele netwerk.

Gebruik de volgende waarden:

* De *-Gateway type* voor een site-naar-site-configuratie is *VPN*. Het gatewaytype is altijd specifiek voor de configuratie die u implementeert. Andere gatewayconfiguraties vereisen misschien -GatewayType ExpressRoute.
* Het *-VpnType* kan *RouteBased* (in sommige documentatie een dynamische gateway genoemd) of *PolicyBased* (in sommige documentatie een statische gateway genoemd) zijn. Voor meer informatie over VPN-gatewaytypen raadpleegt u [Over VPN Gateway](vpn-gateway-about-vpngateways.md).
* Selecteer de gateway-SKU die u wilt gebruiken. Voor bepaalde SKU's gelden configuratiebeperkingen. Zie [Gateway-SKU's](vpn-gateway-about-vpn-gateway-settings.md#gwsku) voor meer informatie. Als er een fout optreedt bij het maken van de VPN-gateway met betrekking tot de GatewaySku, controleert u of u de nieuwste versie van de PowerShell-cmdlets hebt geïnstalleerd.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

Nadat u deze opdracht hebt uitgevoerd, duurt het maximaal 45 minuten voordat de gatewayconfiguratie is voltooid.

## <a name="6-configure-your-vpn-device"></a><a name="ConfigureVPNDevice"></a>6. uw VPN-apparaat configureren

Voor site-naar-site-verbindingen met een on-premises netwerk is een VPN-apparaat vereist. In deze stap configureert u het VPN-apparaat. Bij het configureren van uw VPN-apparaat hebt u de volgende items nodig:

- Een gedeelde sleutel. Dit is dezelfde gedeelde sleutel die u opgeeft wanneer u uw site-naar-site-VPN-verbinding maakt. In onze voorbeelden gebruiken we een eenvoudige gedeelde sleutel. We raden u aan een complexere sleutel te genereren.
- Het openbare IP-adres van de gateway van uw virtuele netwerk. U kunt het openbare IP-adres weergeven met behulp van Azure Portal, PowerShell of de CLI. Gebruik het volgende voor beeld om het open bare IP-adres van uw virtuele netwerk gateway te vinden met behulp van Power shell. In dit voor beeld is VNet1GWPIP de naam van de open bare IP-adres resource die u in een eerdere stap hebt gemaakt.

  ```azurepowershell-interactive
  Get-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="7-create-the-vpn-connection"></a><a name="CreateConnection"></a>7. de VPN-verbinding maken

Maak vervolgens de site-naar-site-VPN-verbinding tussen de gateway van uw virtuele netwerk en het VPN-apparaat. Zorg dat u de waarden vervangt door die van uzelf. De gedeelde sleutel moet overeenkomen met de waarde die u hebt gebruikt voor de configuratie van uw VPN-apparaat. Het '-ConnectionType' voor site-naar-site is **IPsec**.

1. Stel de variabelen in.
   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```

2. Maak de verbinding.
   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
   -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

Na een korte tijd wordt de verbinding tot stand gebracht.

## <a name="8-verify-the-vpn-connection"></a><a name="toverify"></a>8. Controleer de VPN-verbinding

Er zijn een aantal verschillende manieren om uw VPN-verbinding te controleren.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Verbinding maken met een virtuele machine

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]


## <a name="to-modify-ip-address-prefixes-for-a-local-network-gateway"></a><a name="modify"></a>IP-adresvoorvoegsels wijzigen voor de gateway van een lokaal netwerk

Als de IP-adresvoorvoegsels die u wilt routeren naar de locatie van uw on-premises wijzigen, kunt u de gateway van het lokale netwerk wijzigen. Wanneer u deze voor beelden gebruikt, wijzigt u de waarden zodat deze overeenkomen met uw omgeving.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address-for-a-local-network-gateway"></a><a name="modifygwipaddress"></a>Het IP-adres van de gateway wijzigen in dat van een lokale netwerkgateway

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="to-delete-a-gateway-connection"></a><a name="deleteconnection"></a>Een gateway verbinding verwijderen

Als u de naam van uw verbinding niet weet, kunt u deze vinden met behulp van de cmdlet ' Get-AzVirtualNetworkGatewayConnection '.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
-ResourceGroupName TestRG1
```

## <a name="next-steps"></a>Volgende stappen

*  Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Virtuele machines](../index.yml) voor meer informatie.
* Voor meer informatie over BGP raadpleegt u [BGP Overview](vpn-gateway-bgp-overview.md) (BGP-overzicht) en [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (BGP configureren).
* Zie [Een site-naar-site-VPN-verbinding maken](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/) voor meer informatie over het maken van een site-naar-site-VPN-verbinding met behulp van een Azure Resource Manager-sjabloon.
* Zie [HBase-geo-replicatie implementeren](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/) voor meer informatie over het maken van een vnet-naar-vnet-VPN-verbinding met behulp van een Azure Resource Manager-sjabloon.